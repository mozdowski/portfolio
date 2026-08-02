# Portfolio — mozdowski

Six production-grade products, built solo, end to end: product design, backend, frontend, ML/AI, security, compliance and ops. The source is private — these are commercial SaaS candidates — but each case study below documents the architecture and engineering decisions, and **read access to any repo can be arranged for interview processes on request**.

## Projects

| Project | One-liner | Stack highlights |
|---|---|---|
| [Vetty](projects/vetty.md) | AI veterinary triage for clinics, with a mandatory vet-in-the-loop release gate | FastAPI, Gemini/Vertex AI (EU-pinned), CLIP, eval harness with LLM-judge consensus |
| [AIdwokat](projects/ai-dwokat.md) | Regulatory-change early warning for Polish businesses (*vacatio legis* window) | FastAPI, pgvector, hybrid RAG + reranking, Celery, Anthropic Message Batches |
| [i-ventiu](projects/i-ventiu.md) | Configurator-first event planning — PC-part-picker mechanics for events | Django, OR-Tools CP-SAT solver, PostGIS, React, custom design system |
| [Wedding Master Plan](projects/wedding-master-plan.md) | Wedding planning command center with a venue cost engine and seating chart | React, FastAPI, field-level encryption, GDPR subsystem, Stripe |
| [Pełnia](projects/sovereign.md) | Privacy-first symptothermal fertility tracker — no network layer at all | Flutter, SQLCipher, on-device LSTM (TFLite), Bayesian inference |
| [Unbound Mind](projects/unbound-mind.md) | Offline-first OCD self-help app with hardware-bound encryption | Flutter, native AndroidKeyStore key management, AES-GCM, zero INTERNET permission |

## Recurring themes

**Security & privacy engineering.** Field-level encryption with zero-downtime key rotation; refresh-token rotation with theft detection; hardware-bound keys behind live biometric checks; two apps that ship with *no network permission whatsoever*, making their privacy claims externally verifiable; fail-closed input gatekeeping for LLM pipelines.

**Evaluation-driven AI.** Both AI products gate changes on frozen golden datasets with measured metrics — triage accuracy 55% → 77% in one, retrieval recall@10 0.78 → 0.96 in the other — including honestly recorded negative results and eval-discovered production bugs.

**Compliance as code, not documentation.** GDPR data-subject rights as real tested endpoints, consent hashing, retention sweeps, EU data-residency pinning enforced by tests, EU AI Act machine-readable marking.

**Test depth unusual for solo work.** Roughly 1,500+ automated tests across the six projects, with test-to-source ratios approaching 1:1 in several, regression tests named after specific incidents, and self-verifying demo pipelines where a recorded marketing video doubles as a passing integration test.

## Open source

Selected generic pieces extracted from these projects:

- [ssh-control-mcp](https://github.com/mozdowski/ssh-control-mcp) — MCP server for SSH control
- *More extractions in progress — mirrored Python/TypeScript Sentry PII scrubbers, an auth-bound key store for Flutter, a retrieval eval harness, and a Playwright narrated-demo recorder are queued for release.*

## Contact

GitHub: [@mozdowski](https://github.com/mozdowski) · Email: vasco.ozdowski829@gmail.com
