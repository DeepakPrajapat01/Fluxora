# Delivery Roadmap

Work phase by phase. Do not advance just because code compiles; advance when acceptance criteria pass.

| Phase | Name | Main outcomes | Exit gate |
|---|---|---|---|
| 0 | Planning and repository setup | Requirements, decisions, repo conventions, initial backlog | Scope and architecture approved |
| 1 | Foundation | Spring Boot and React skeletons, Docker Compose, PostgreSQL migrations, CI, health checks | Fresh checkout starts; CI green |
| 2 | Identity and tenant boundaries | Users, roles, tenant membership, JWT/session strategy, authorization tests | Cross-tenant and role tests pass |
| 3 | Shipment core | Shipment CRUD, pricing estimate, status history, validation, API docs | Core shipment journey works end-to-end |
| 4 | Dispatch and concurrency | Partner availability, assignment offers, atomic accept, race tests | Exactly one active accepted assignment in race tests |
| 5 | Event reliability | Outbox, Kafka publisher, event schema, idempotent consumer, retries/DLQ | Kafka outage and duplicate-event tests pass |
| 6 | Tracking and real time | Location ingestion, stale-update policy, Redis, SSE | Authorized live tracking works and recovers |
| 7 | Operations and observability | Metrics, dashboards, correlation IDs, audit events, delay queries | Metrics visible; failure can be diagnosed |
| 8 | Resilience and load testing | k6 scenarios, fault injection, benchmark report | Repeatable test report with environment and results |
| 9 | AI operations assistant | Read-only tools, scoped queries, prompt-injection defenses, audit | AI cannot bypass permissions or mutate data |
| 10 | Deployment and portfolio polish | Container hardening, optional Kubernetes, CI/CD, docs, demo script | Reproducible deployment and interview walkthrough |

## Suggested 8–12 week pacing

This is an estimate, not a commitment. A reasonable plan is to spend the first two weeks on foundations and core domain work, then build assignment correctness and event reliability, followed by tracking, observability, testing, and polish. If time is limited, prioritize phases 1–8 and treat AI/Kubernetes as stretch goals.

## Prioritization rule

For every feature ask:
1. Which user problem does it solve?
2. What invariant or behavior must remain true?
3. How will it be tested?
4. What happens if a dependency fails?
5. What evidence can be shown in an interview?
