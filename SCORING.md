# Scoring

Final score is out of 100 and has five parts.

| Part | Weight | Source |
| --- | --- | --- |
| AI review score | 30 | Lokum leaderboard: average overall score across your merged PRs |
| Custom criteria | 25 | Lokum leaderboard: average of the six hackathon criteria below |
| Feature coverage | 25 | Judges: finished tickets, weighted by tier |
| Demo | 10 | Judges: live walkthrough of the booking flow |
| Innovation | 10 | Judges: stretch tickets and ideas beyond the board |

The first two parts are fully automatic and update on every merge, so the leaderboard is live during the event.

## 1. AI review score (30)

Every merged PR is reviewed by Lokum's AI reviewer. It reads the diff, commits, PR title and description, review comments and the linked ticket, then scores seven built-in dimensions:

| Dimension | Weight in overall | What moves it |
| --- | --- | --- |
| Code quality | 25% | Readability, naming, modularity, duplication |
| Implementation logic | 20% | Correct and complete against the ticket, edge cases |
| AI review attempts | 15% | Based on the AI review runs on the PR before it merged |
| PR structure | 10% | Branch name, title, description, attachments |
| Peer review activity | 10% | Quality of review discussion on the PR |
| Merge closure | 10% | No unresolved threads, conflicts or loose ends |
| Commit efficiency | 10% | Focused commits with meaningful messages |

Your team's number is the average across all merged PRs. A few excellent PRs can outscore many sloppy ones.

## 2. Custom criteria (25)

On top of the built-in dimensions, each PR is scored 0 to 100 against six criteria written for this project. They are averaged separately on the leaderboard and do not change the overall AI score. They are deliberately different from the built-in dimensions, so nothing is counted twice.

| Key | Criterion | What the reviewer looks for |
| --- | --- | --- |
| `parking_domain_rules` | Parking Domain Rules | No double booking for overlapping windows, vehicle type fits the spot, cancelled/expired reservations free the spot, valid time windows, charges from real duration |
| `api_design` | API Design | Nouns under `/api`, correct verbs, precise status codes (201, 204, 400, 404, 409), DTOs not entities, pagination, one error shape, documented in OpenAPI |
| `data_modeling_and_integrity` | Data Modeling and Integrity | Sensible entities and relations, database constraints and indexes, BigDecimal for money, UTC times, `@Transactional` writes, concurrency-safe bookings |
| `security` | Security | Auth on protected endpoints, server-side role and ownership checks, hashed passwords, secrets from env, validated input, no leaked stack traces |
| `testing` | Testing | Tests that fail when behaviour breaks, edge cases from the ticket, at least one controller test asserting status and body, green CI |
| `production_readiness` | Production Readiness | Externalised config, safe and useful logging, migrations, OpenAPI docs, still runs in Docker Compose |

A PR that has nothing to do with a criterion scores a neutral 70 on it, so small focused PRs are not penalised. `testing` is the exception: every PR that adds behaviour is expected to add tests.

The machine-readable version is [lokum/scoring-criteria.json](lokum/scoring-criteria.json).

## 3. Feature coverage (25)

Judges check which tickets work on `main` at the deadline. Each tier from [HACKATHON.md](HACKATHON.md) scores the share of its tickets that are done, multiplied by its weight.

| Tier | Weight |
| --- | --- |
| 1 · Foundation | 15 |
| 2 · Core booking flow | 35 |
| 3 · Users and security | 25 |
| 4 · Payments | 15 |
| 5 · Production readiness | 10 |

Coverage = Σ (tier weight × tickets done in tier ÷ tickets in tier), then scaled to 25.

A ticket counts as done when its endpoint works as the ticket describes, is merged to `main` through a PR, and has at least one test. Half-built tickets count as zero.

## 4. Demo (10)

| Points | Looks like |
| --- | --- |
| 9 to 10 | Full flow from the brief works live, including the 409 on a double booking and an admin-only view. Clear story, on time |
| 6 to 8 | Booking flow works with small gaps or one scripted step |
| 3 to 5 | Some endpoints shown in isolation, flow not connected |
| 0 to 2 | Nothing runs live |

## 5. Innovation (10)

Stretch tickets from the board, plus anything the team invents: dynamic pricing, live availability over WebSocket, spot recommendations, a public deployment, a small frontend. Judges reward one feature done well over three sketched.

## Tie-breakers

1. Higher `parking_domain_rules` average
2. Higher Tier 2 coverage
3. Fewer PRs merged without a review comment

## Penalties

| Issue | Deduction |
| --- | --- |
| Secret or credential committed to the repo (even if later removed) | −5 |
| `main` does not build at the deadline | −10 |
| Commits pushed after the deadline | Ignored, and −5 if they were force-pushed over history |

---

## For organisers: setting it up in Lokum

The criteria resolve from most specific to least: team, then job listing, then project template, then organisation. Set them once on the template and every hackathon team inherits them.

1. Open the Parking Lot System project in Lokum and edit it.
2. In the scoring criteria editor add the six criteria. Paste each name and description from `lokum/scoring-criteria.json`. The key is generated from the name, so type the names exactly as written and the keys will match.
3. Save. Teams created from the template inherit the criteria, and their scoring criteria panel shows the source as the template.
4. To give one team different criteria, set an override on that team. Clearing the override puts it back on the template's criteria.

Also set on the project before the event:

- Repository link required: on (it already is), pointing at each team's copy of this template.
- Link required for tickets: on (it already is for this project), so every PR maps to a ticket and the implementation score has a rubric.
