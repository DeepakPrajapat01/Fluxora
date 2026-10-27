# Scope, Assumptions, and Non-Goals

## MVP scope

The MVP is complete when a user can create an account, create a shipment, see shipment history, and move a shipment through a validated assignment and delivery workflow. It must include automated tests, local infrastructure, and basic operational metrics.

### Must have
- Authentication and role-based authorization.
- Tenant-aware data model and repository/service checks.
- Shipment create/read/list and status history.
- Assignment workflow with concurrency protection.
- Delivery partner availability and status updates.
- PostgreSQL migrations.
- API validation and consistent error responses.
- Docker Compose local environment.
- Unit and integration tests.
- Basic React UI for customer, partner, and admin journeys.
- CI that builds and tests the project.

### Should have
- Redis-backed latest-location state.
- SSE tracking updates.
- Kafka events and transactional outbox.
- Idempotent consumer and retry/dead-letter strategy.
- Prometheus/Grafana dashboards.
- Load tests with recorded results.

### Could have
- AI operations assistant.
- Kubernetes deployment manifests.
- Advanced operational analytics.
- Map visualization and ETA estimation.

## Explicit non-goals
Do not start by splitting the system into many microservices. Do not add technologies solely to make the stack look impressive. Do not claim high availability, exactly-once processing, real-time guarantees, or performance gains without a test and a clear definition.

## Assumptions
- Initial deployment is a single-region demo.
- PostgreSQL is the source of truth for business state.
- Kafka provides asynchronous event distribution, not the only durable record of a business action.
- Redis is disposable cache/live state and can be rebuilt or allowed to expire.
- The frontend is a browser application.
- All external services can be replaced by local development containers or deterministic test doubles.
