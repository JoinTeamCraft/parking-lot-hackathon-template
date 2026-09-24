# Tips and Tricks

Things that win hackathons like this one. Most of them are about scope and process, not clever code.

## Strategy

- **Finish Tier 2 before touching anything shiny.** The core booking flow carries the biggest coverage weight and is what the demo shows. A working booking flow with no payments beats a half-working everything.
- **Spend the first 90 minutes on the entity model together.** Draw `User`, `Vehicle`, `ParkingLot`, `Floor`, `ParkingSpot`, `Reservation`, `Payment` on one whiteboard, agree on field names and enums, and merge PLS-003 before anyone writes a controller. Half the merge conflicts in team hackathons come from two people inventing `ParkingSpot` differently.
- **Split by vertical slice, not by layer.** One person owns "parking lots" end to end (entity, repo, service, controller, tests). Don't have one person do all the controllers and another all the services; they block each other.
- **Put one person on the foundation early.** The global exception handler (PLS-012) and validation (PLS-039) make every later PR easier and every later API design score higher.
- **Keep a running demo script from hour one.** A `docs/demo.http` file that you add to as features land. At H46 you run it, not write it.
- **Merge the easy duplicates once.** PLS-018 and PLS-035 are the same feature, and so are PLS-020 and PLS-021. Build once and mention both.

## Scoring well on the AI review

The reviewer can only score what it sees in the PR. Make it easy.

- **One ticket per PR.** Put the ticket number in the title: `PLS-017: Book a parking spot`. Mixed PRs muddy the implementation score and the custom criteria.
- **Fill in the PR template.** A good description (what, why, how tested) is almost free PR structure points. Paste a real `curl` and its response. That counts as an attachment.
- **Name branches after tickets.** `feature/PLS-017-book-spot` beats `fix2`.
- **Review each other's PRs with real comments.** Peer review is 10% of the overall score. "LGTM" is not review. Ask a question, suggest a test, point at an edge case. Resolve every thread before merging.
- **Commit like someone will read it.** `Add overlap check to reservation service` beats `wip`, `fix`, `fix again`. Squash noise locally before pushing if you need to.
- **Small PRs merge faster and score higher.** Aim for under 400 changed lines. Big diffs hide bugs and tire the reviewer, human or AI.
- **Keep generated and unrelated files out.** No `build/`, IDE folders or mass reformatting in feature PRs.

## Spring Boot, done right

### Preventing double booking

This is the single most important piece of logic. An `if (isAvailable)` check is not enough; two requests can pass it at the same time.

Pick one:

```java
// 1. Pessimistic lock on the spot row while you check and insert
@Lock(LockModeType.PESSIMISTIC_WRITE)
@Query("select s from ParkingSpot s where s.id = :id")
Optional<ParkingSpot> findByIdForUpdate(@Param("id") Long id);
```

```sql
-- 2. Let Postgres refuse overlaps outright (needs the btree_gist extension)
ALTER TABLE reservation ADD CONSTRAINT no_overlap
  EXCLUDE USING gist (spot_id WITH =, tstzrange(start_time, end_time) WITH &&)
  WHERE (status IN ('PENDING', 'CONFIRMED'));
```

The overlap test itself is `existing.start < requested.end AND requested.start < existing.end`. Return **409 Conflict** when it fails.

### Other domain gotchas

- Money is `BigDecimal`, never `double`. Round with `RoundingMode.HALF_UP` at the end, not along the way.
- Store times as `Instant` or `OffsetDateTime` in UTC. The ticket payloads have no zone; decide what that means and write it in the README.
- Validate `endTime > startTime` and `startTime` not in the past with a custom constraint or in the service.
- Use enums for `VehicleType`, `SpotType`, `ReservationStatus`, `PaymentStatus`, stored with `@Enumerated(EnumType.STRING)`. Ordinal enums break when someone reorders them.
- Charge by duration, rounded up to the billing unit you choose (for example per started hour). Put the rate in config, not in code.

### API hygiene

- Controllers return DTOs. Java `record`s make them one line each.
- `POST` returns `201 Created` with a `Location` header. `DELETE` returns `204`.
- One `@RestControllerAdvice` maps exceptions to a single error body. Try Spring's built-in `ProblemDetail`.
- Paginate list endpoints with `Pageable`. It is one parameter.
- Add `@Operation` and `@Schema` annotations as you go; Swagger UI is already wired at `/swagger-ui.html`.

### Security

- `spring-boot-starter-security` plus a `SecurityFilterChain` bean. Start with everything authenticated and open up only `/api/auth/**`, `/actuator/health` and the Swagger paths.
- Hash with `BCryptPasswordEncoder`. Never log a password or token, not even at debug.
- JWT secret comes from `JWT_SECRET`. Never paste a real one in `application.yml`.
- Check ownership, not only role: a customer may only see and cancel their own reservations. `@PreAuthorize` with a service method works well.
- Logout with stateless JWT means a denylist or short-lived tokens. Pick one and explain it in the PR.

### Testing fast

- `@WebMvcTest` for controllers with the service mocked: fast, and asserts status codes and JSON.
- `@DataJpaTest` for repository queries such as the overlap query.
- Plain JUnit and Mockito for service logic. This is where the edge-case tests live.
- The test profile already uses H2 in PostgreSQL mode. If you use Postgres-only features like the `EXCLUDE` constraint, add Testcontainers for those tests.
- Write the overlap test first. It is the one the judges will ask about.

## Using AI assistants

Allowed, and they help. They also produce the exact code the reviewer marks down.

- Ask for one ticket at a time and paste the ticket text. Vague prompts get vague code.
- Read every line before you commit it. Delete unused imports, placeholder comments and extra endpoints nobody asked for.
- Assistants love returning entities from controllers, catching `Exception`, and skipping ownership checks. Look for those three every time.
- Ask the assistant for the tests and edge cases too, then check the tests would actually fail if you broke the code.

## Git hygiene

- Protect `main`: require a PR, one approval and a green CI check.
- Rebase or merge `main` into your branch often. Small conflicts at H10 beat a huge one at H45.
- Never commit `.env`. It is already in `.gitignore`; keep it there.
- If a secret leaks, rotate it immediately. Deleting the commit does not unpublish it.

## Demo

- Rehearse once, end to end, against a fresh database. Most demo failures are stale data.
- Seed data with a `CommandLineRunner` behind a `demo` profile so the lots and spots exist before you start.
- Show the 409 on the double booking. It proves the hardest part works.
- Have a recording as backup in case Wi-Fi dies.
- Finish on time. Judges remember the team that ran over.

## Last hours checklist

- [ ] `./gradlew build` passes on a clean clone
- [ ] `docker compose --profile app up --build` starts the whole thing
- [ ] README lists finished tickets, deviations and team members
- [ ] `docs/` has the demo requests
- [ ] No open PRs you meant to merge
- [ ] No secrets in history (`git log -p | grep -i -E "secret|password|api[_-]?key"`)
