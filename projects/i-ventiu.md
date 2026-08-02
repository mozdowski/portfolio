# i-ventiu — configurator-first event planning platform

> **Status:** Private, pre-launch · **Code:** private, available on request · **Role:** solo design, development & product

Universal event planner built on a simple insight: **PC-part-picker mechanics applied to event planning.** An event is a canvas of service slots — venue, catering, photography, music, decor. As slots fill, a category suggestion graph recommends related services, warns about missing dependencies and conflicts, and a constraint solver distributes the budget across slots. Two-sided: planners configure events; providers self-publish listings and receive quote requests. Guest-facing RSVP is tokenized and login-free, with dietary preferences aggregating live into the catering slot's headcount.

## Tech stack

| Layer | Technologies |
|---|---|
| Backend | Python 3.12, Django 5 + DRF, PostgreSQL 16 + PostGIS, OR-Tools (CP-SAT), UUIDv7 keys, JSON-Schema-validated JSONB |
| Frontend | React 18, TypeScript, Vite, TanStack Query, i18next (PL/EN), fully typed generated API client (`openapi-typescript`) |
| Design | No UI framework — ~4,000 LOC hand-written CSS implementing a custom "Polish School of Posters" design system |
| Testing | pytest (426 tests), vitest (185), Playwright (43 E2E incl. visual regression against committed baselines) |

## Engineering highlights

**Budget auto-fit as a constraint-satisfaction problem.** The headline feature is formally a multiple-choice knapsack with side constraints, solved exactly with OR-Tools CP-SAT: at most one listing per unfilled slot, maximize quality subject to the budget, locked slots fixed, pairwise compatibility constraints enforced. Required slots dominate the objective so mandatory services get filled before nice-to-haves when money runs short. The solver produces three de-duplicated plans at different trade-offs (tight / best value / premium).

**An ethics constraint, enforced in code.** *Paid promotion never enters the objective function.* Boosted listings can enter the candidate shortlist (disclosed as promoted, per EU platform-to-business rules), but the solver optimizes on a separate boost-free quality score — otherwise "the best plan for your budget" silently becomes "the best plan for our revenue." The two scores are distinct fields in the type, and the solver structurally cannot see the boosted one.

**Tri-state rule evaluation with deliberately opposite failure modes.** A single tiny predicate evaluator returns true / false / *indeterminate*. Two rule systems share it and resolve indeterminacy in opposite directions: advisory suggestions **fail open** (never block a user's canvas on missing data), while autofit compatibility enforcement **fails closed** (never auto-book a vendor that can't be proven compatible). Same engine, opposite defaults, each matched to its consequence of being wrong.

**End-to-end type safety.** The OpenAPI schema is exported from Django and code-generated into TypeScript — change a serializer and the frontend build breaks. Business logic lives in service layers with pure-computation modules deliberately separated from I/O, so the ranking and suggestion engines unit-test without a database.

**Self-verifying marketing videos.** A Playwright pipeline records narrated, subtitled 1080p demo clips with simulated cursor and human pacing — and each scenario asserts its flow's key business outcome before delivering the clip. A video that records is a flow that works; a broken flow exits non-zero and no video ships. Marketing collateral that doubles as an integration test.

**Data-model decisions built for the roadmap.** One slot-assignment table handles both user-typed vendors and platform listings via two nullable FKs and a DB CHECK constraint that exactly one is set — so the configurator UI won't change when the marketplace phase lands. Event templates are admin-editable data (JSONB), never code. Money is integer grosze everywhere.

## Design system

"Afisz" — a custom visual language drawn from the Polish School of Posters: named color tokens, Syne / Schibsted Grotesk / Space Mono typography, hard offset shadows, rotated poster blocks, suggestions rendered as literal red string pinned between poster blocks on a corkboard. Deliberately does not look like another Tailwind SaaS.

## Scale & quality

~33,000 lines of tracked code (12,000 Python excluding migrations, 10,500 hand-written TypeScript, 4,000 CSS), 654 automated tests across three layers, visual-regression baselines committed to git, modular-monolith boundaries enforced by convention and honored in code.

## Screenshots & demo

*Coming soon.*
