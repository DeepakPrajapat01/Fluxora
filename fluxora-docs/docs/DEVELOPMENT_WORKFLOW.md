# Development Workflow

## Repository layout (initial proposal)

```text
fluxora/
├── backend/
├── frontend/
├── infra/
│   ├── docker/
│   ├── prometheus/
│   └── grafana/
├── docs/
│   ├── adr/
│   └── phases/
├── scripts/
├── .github/workflows/
├── .env.example
├── .gitignore
├── docker-compose.yml
└── README.md
```

## Branch and commit habits
- Use small feature branches, e.g. `feat/shipment-create`, `test/assignment-race`.
- Use clear commits such as `feat: add shipment creation API`.
- Keep secrets and local environment files out of version control.
- Do not combine unrelated refactors with feature work.

## Definition of Done
A task is done only when:
- Acceptance criteria are met.
- Relevant automated tests pass.
- Input validation and authorization are covered.
- Errors are predictable and safe.
- Logs/metrics are adequate for the behavior.
- API/docs are updated.
- Local setup still works from documented commands.
- No secrets or real personal data were introduced.

## Pull request checklist
- [ ] Problem and intended behavior described.
- [ ] Design choice/trade-off explained if material.
- [ ] Tests added or updated.
- [ ] Failure and concurrency cases considered.
- [ ] Migration included where schema changes.
- [ ] Tenant isolation and authorization considered.
- [ ] Metrics/logging considered.
- [ ] Screenshots or API examples included when useful.

## Local configuration
Provide `.env.example` with safe placeholders only. Real `.env` stays untracked. Prefer Docker Compose for local PostgreSQL, Redis, and Kafka so teammates/interviewers can reproduce the environment.
