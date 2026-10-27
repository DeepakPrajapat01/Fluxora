# Fluxora — Fault-Tolerant, Event-Driven Logistics Platform

Fluxora is a portfolio-grade, full-stack logistics application for managing shipments, delivery assignments, and tracking. Its central engineering goal is to demonstrate **correctness under concurrency, reliable event processing, graceful failure handling, and measurable performance**.

## Documentation map

- [Product requirements](docs/PRODUCT_REQUIREMENTS.md)
- [Scope and non-goals](docs/SCOPE.md)
- [Phased roadmap](docs/ROADMAP.md)
- [Architecture](docs/ARCHITECTURE.md)
- [Domain model](docs/DOMAIN_MODEL.md)
- [API conventions](docs/API_CONVENTIONS.md)
- [Security and tenant isolation](docs/SECURITY.md)
- [Event-driven design](docs/EVENT_DRIVEN_DESIGN.md)
- [Reliability and failure scenarios](docs/RELIABILITY.md)
- [Testing strategy](docs/TESTING_STRATEGY.md)
- [Observability and performance](docs/OBSERVABILITY.md)
- [Frontend and UX](docs/FRONTEND.md)
- [AI operations assistant](docs/AI_ASSISTANT.md)
- [Development workflow](docs/DEVELOPMENT_WORKFLOW.md)
- [Architecture decision records](docs/adr/README.md)
- [Phase 1 checklist](docs/phases/PHASE_1_FOUNDATION.md)

## First release principle

Build a **modular monolith first**, with clear module boundaries and asynchronous processing through Kafka where it adds value. Do not begin with many independently deployed microservices. A well-tested, observable system with evidence of correctness is stronger than a large stack of partially working technologies.

## Initial stack

- Backend: Java + Spring Boot
- Frontend: React + TypeScript
- Primary data store: PostgreSQL
- Cache/live state: Redis
- Event streaming: Apache Kafka
- Authentication/authorization: Spring Security + JWT
- Real-time updates: Server-Sent Events (SSE)
- Tests: JUnit 5, Mockito, Testcontainers, k6
- Local infrastructure: Docker Compose
- Deployment path: Docker, Kubernetes, GitHub Actions
- Observability: Actuator, Micrometer, Prometheus, Grafana
- AI: LLM API with server-side, allow-listed tools and authorization checks

## Working rules

1. Keep this documentation aligned with implementation.
2. Deliver one phase at a time; each phase must have acceptance criteria.
3. Prefer measurable claims over unsupported claims such as “70% faster.”
4. Never put secrets, real customer data, tokens, or credentials in the repository.
5. Record significant architectural trade-offs in ADRs.
6. Do not introduce Kafka, Redis, Kubernetes, or an LLM into a path until the use case and failure behavior are understood.
