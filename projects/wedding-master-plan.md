# Wedding Master Plan — wedding planning command center

> **Status:** Private MVP (planned SaaS) · **Code:** private, available on request · **Role:** solo design, development & ops

A wedding planning "command center" for couples — built to replace the fragmented spreadsheets most couples actually plan with. Polish-language UI, monetized as a one-time premium unlock per wedding via Stripe.

## What it does

- Onboarding wizard → dashboard with countdown and budget health
- Auto-generated task timeline based on the wedding date
- Guest CRM with RSVP tracking and dietary information
- Budget line items and vendor tracking with per-category fields
- **Venue comparison engine** — "real cost per guest" across venues, with age-banded plate pricing and a live what-if simulator: toggle guest tiers on/off and watch total cost and cost-per-head recompute in real time, including seasonal price variants for the same venue
- **Drag-and-drop seating chart** with touch/pinch-zoom support
- Multi-wedding support with automatic archival

## Tech stack

| Layer | Technologies |
|---|---|
| Frontend | React 18, TypeScript, Vite, TanStack Query, React Hook Form + Zod, Tailwind + shadcn/ui, PWA |
| Backend | FastAPI, SQLAlchemy 2, Alembic, PostgreSQL 16, Stripe, APScheduler |
| Testing | Vitest + Testing Library, pytest, Playwright |
| Infra | Docker Compose, Caddy (TLS), systemd-timed encrypted backups, Sentry |

## Engineering highlights

**Field-level encryption at rest with zero-downtime key rotation.** Guest PII (names, contact details, dietary notes) is encrypted in PostgreSQL via custom SQLAlchemy `TypeDecorator`s over `MultiFernet` — the newest key encrypts, all keys decrypt, so keys rotate without downtime or bulk re-encryption. The module documents its own invariants (e.g. never filter server-side on an encrypted column — it would match ciphertext).

**Refresh-token rotation with family-based reuse detection.** Short-lived access JWTs plus 30-day rotating refresh tokens stored hashed, delivered in HttpOnly `SameSite=Strict` cookies. Every token carries a family ID; presenting an already-revoked token is treated as theft and revokes the whole family. On the client, concurrent refreshes collapse into a single in-flight promise so parallel tabs can't stampede a rotating endpoint.

**GDPR/RODO as a real subsystem, not a checkbox.** Consent records and data-subject-request logs survive account erasure (audit trail outlives the user), account deletion auto-logs an erasure DSR, payments are anonymised rather than deleted for tax retention (Art. 17(3)(b)), and a daily retention job archives and purges on rolling windows. PII scrubbers for Sentry are implemented twice — mirrored pure functions in Python and TypeScript, both unit-tested — stripping headers, bodies, cookies and identity before events leave the process. Backups are `pg_dump` piped through `age` encryption to EU object storage; plaintext never touches disk.

**Test depth unusual for a solo project.** 369 test cases across three suites: 74 backend (pytest), 241 frontend unit (Vitest), 54 end-to-end (Playwright) — including dedicated tests for encryption, refresh-token security, retention jobs, error-report scrubbing and payment gating.

## Scale

~19,500 lines of application code (excluding vendored UI components) across a React SPA and a layered FastAPI backend (routers → repositories → models, with a generic typed repository base and wedding-scoped access control).

## Screenshots

*Coming soon.*
