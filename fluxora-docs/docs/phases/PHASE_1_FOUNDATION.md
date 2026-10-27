# Phase 1 — Foundation

**Goal:** Create a reproducible development environment and a minimal backend/frontend skeleton before implementing business features.

## Deliverables
- Repository structure established.
- Java/Spring Boot backend builds and starts.
- React/TypeScript frontend builds and starts.
- PostgreSQL starts locally through Docker Compose.
- Database migration tool configured; initial migration applied.
- Backend health endpoint available.
- Frontend can call a backend health/status endpoint.
- `.env.example` and `.gitignore` added.
- CI workflow builds and tests the backend and frontend.
- README contains exact setup and run commands.

## Suggested decisions
- Choose a supported Java LTS version and compatible Spring Boot release.
- Use Maven or Gradle; select one and stay consistent.
- Use Flyway or Liquibase for migrations; do not mix migration tools.
- Keep one backend deployable initially.
- Add Redis/Kafka to local Compose when the relevant phase begins, or include them early only if setup remains reliable and documented.

## Tasks
- [ ] Initialize Git repository.
- [ ] Create `backend/`, `frontend/`, `infra/`, `docs/`, and `scripts/`.
- [ ] Generate Spring Boot application with Actuator and validation.
- [ ] Add a minimal status endpoint and consistent error handling baseline.
- [ ] Generate React + TypeScript app.
- [ ] Add frontend API configuration using environment variables.
- [ ] Add PostgreSQL service and health check to Docker Compose.
- [ ] Configure schema migrations.
- [ ] Add backend unit-test and frontend build/test commands.
- [ ] Add CI workflow.
- [ ] Document local setup and troubleshooting.

## Acceptance criteria
- A clean clone can follow the README to start the required services.
- Backend starts and reports healthy.
- Frontend starts and can display backend health.
- Database migration runs from an empty database.
- Tests/builds pass locally and in CI.
- No credentials are committed.
- All versions and commands are documented.

## Exit checklist
Do not begin Phase 2 until every acceptance criterion is checked or a deliberate exception is recorded in an ADR.
