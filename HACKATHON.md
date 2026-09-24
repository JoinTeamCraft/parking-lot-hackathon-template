# Hackathon Brief

## The challenge

Ship a working, secure, tested backend for a parking lot system. The Lokum project has 62 tickets, far more than any team can finish. Choosing what to build is part of the challenge: a team that ships the core booking flow well beats a team that half-builds everything.

## Teams

- 3 to 5 people per team.
- One repo per team, created from this template.
- Everyone commits under their own GitHub account. Individual contribution is visible on Lokum.

## Rules

1. All code is written during the event. Libraries and frameworks are fine; copying a finished parking lot project is not.
2. AI assistants are allowed. You are scored on the code, so you own every line you merge.
3. Every change reaches `main` through a pull request that names a ticket.
4. No secrets in the repo. A committed API key or password costs points.
5. The final state of `main` at the deadline is what gets judged. Pushes after the deadline are ignored.

## Timeline

Written for 48 hours. Scale the blocks for 24 or 72.

| Time | What happens |
| --- | --- |
| H0 | Kickoff, brief, teams create repos and join the Lokum project |
| H0 to H2 | Planning: split tickets, agree on the entity model, set up branch protection |
| H2 | Foundation tier merged (skeleton, entities, exception handling) |
| H24 | Checkpoint: mentors review each team for 10 minutes |
| H44 | Code freeze warning |
| H46 | Code freeze. Only README and demo prep after this |
| H48 | Demos, 5 minutes per team + 3 minutes Q&A |

## Scope tiers

Tickets are grouped by how much they matter. Finish a tier before starting the next one; feature coverage in [SCORING.md](SCORING.md) is weighted by tier.

### Tier 1 · Foundation (weight 15)

| Ticket | Title |
| --- | --- |
| PLS-001 | Setup Project Structure |
| PLS-002 | Configure PostgreSQL Database |
| PLS-003 | Design Entity Classes |
| PLS-004 | Define Repository Interfaces |
| PLS-012 | Implement Exception Handling |
| PLS-039 | Implement Data Validation |

PLS-001 and PLS-002 are mostly done by this template. Claim them by adjusting the structure to your team's design.

### Tier 2 · Core booking flow (weight 35)

| Ticket | Title |
| --- | --- |
| PLS-005 to PLS-010 | Parking lot CRUD and search |
| PLS-013 to PLS-016 | Parking spot add, update availability, delete, check availability |
| PLS-017 | Book a Parking Spot |
| PLS-024 to PLS-028 | Reservation service, create, update status, fetch, list |

This is the heart of the project. The hardest and most valuable part is preventing double booking.

### Tier 3 · Users and security (weight 25)

| Ticket | Title |
| --- | --- |
| PLS-018 / PLS-035 | Registration |
| PLS-019 to PLS-021 | Update and delete user |
| PLS-022, PLS-023, PLS-036 | JWT authentication, login, logout |
| PLS-037 | Role-Based Access Control |
| PLS-047 | CORS Configuration |

### Tier 4 · Payments (weight 15)

| Ticket | Title |
| --- | --- |
| PLS-030 to PLS-033 | Create, process, fetch, list payments |
| PLS-034 | Payment Status Notification API |
| PLS-044 | Integrate External Payment Gateway (a sandbox or a mock is fine) |

### Tier 5 · Production readiness (weight 10)

| Ticket | Title |
| --- | --- |
| PLS-029 | Scheduled Tasks for Reservation Cleanup |
| PLS-038 | Setup Logging |
| PLS-045 | Automated Database Migrations (Flyway or Liquibase) |
| PLS-046 | API Documentation |
| PLS-061 | Rate Limiting |

### Stretch · counts toward Innovation

| Ticket | Title |
| --- | --- |
| PLS-011, PLS-042, PLS-043 | Utilisation and reports |
| PLS-053, PLS-054 | Booking history |
| PLS-057 to PLS-060 | Admin overview, statistics, user management |
| PLS-040, PLS-041 | Email notifications |
| PLS-048, PLS-049 | Data export and import |
| PLS-050 to PLS-052, PLS-055, PLS-056 | Feedback and reviews |
| PLS-062 | Recommend Parking Spot |

Ideas beyond the board also count: dynamic pricing for peak hours, a WebSocket live availability feed, multi-language messages, a deploy to a public URL.

### Overlapping tickets

A few tickets describe the same feature twice. Build it once and mention both numbers in the PR.

- PLS-018 and PLS-035 are both registration.
- PLS-020 and PLS-021 are both delete user.
- PLS-017 and PLS-025 both create a booking for a spot and time window. Treat PLS-017 as the endpoint and PLS-025 as the reservation entity and service behind it.

## What to submit

By the deadline, `main` must contain:

1. Code that builds and passes `./gradlew build` in CI.
2. An updated README with: how to run, a list of finished tickets, any deviations from the ticket specs, and team members.
3. A Postman collection or `.http` file in `docs/` covering the demo flow.
4. Optional: a public URL where the API is running.

## Demo

Five minutes. Show the booking flow end to end against the running API:

1. Register and log in as a customer.
2. Search for a lot, check availability, book a spot.
3. Try to book the same spot for an overlapping time and show the 409.
4. Pay for the reservation.
5. Log in as an admin and show something only an admin can see.

Then one minute on what you would build next.
