# Architecture

## Initial architecture: modular monolith plus asynchronous infrastructure

Start with one Spring Boot deployable organized into clear modules. This reduces operational overhead while preserving boundaries that could later be extracted if justified by evidence.

```text
React + TypeScript
        |
     REST / SSE
        |
Spring Boot application
  ├── identity / tenancy
  ├── shipments
  ├── dispatch
  ├── tracking
  ├── operations / audit
  └── AI tools (later, read-only)
        |
  ├── PostgreSQL (source of truth + outbox)
  ├── Redis (cache / latest live location)
  └── Outbox relay -> Kafka -> idempotent consumers
                         |
                    notifications / projections (later)
```

## Responsibilities

### React frontend
Renders role-specific workflows. It is not a security boundary; all authorization is enforced by the backend.

### Spring Boot
Owns business rules, request validation, authorization, transactions, and state transitions. Controllers should be thin; domain decisions belong in application/domain services.

### PostgreSQL
Canonical store for users, tenants, shipments, assignments, status history, audit metadata, and outbox records. Use migrations from the beginning.

### Redis
Caches or stores ephemeral/latest location state when appropriate. Never make Redis the only source of truth for a critical shipment transition. Define TTL, eviction, and rebuild behavior.

### Kafka
Carries domain events asynchronously. Consumers must expect duplicates, retries, rebalances, and delayed delivery. Ordering is only guaranteed within a partition; select a key such as shipment ID when per-shipment ordering is required.

### SSE
Streams server-to-client updates for tracking and operational views. Clients must be able to reconnect and recover from a normal REST snapshot. Use polling as a documented fallback if necessary.

## Module boundaries

Suggested backend packages/modules:
- `identity`
- `tenancy`
- `shipments`
- `dispatch`
- `tracking`
- `events`
- `operations`
- `aiassistant` (late phase)

Avoid circular dependencies. Shared types should be small and intentional.

## Data-flow examples

### Create shipment
1. Authenticate and resolve tenant membership.
2. Validate payload and calculate estimate.
3. In one PostgreSQL transaction, insert shipment, status-history row, and outbox event.
4. Return the committed shipment representation.
5. Outbox relay later publishes the event to Kafka.
6. Consumers process it idempotently.

### Accept assignment
1. Authenticate partner and verify eligibility.
2. Execute a conditional update or transaction with a uniqueness invariant.
3. Only one competing request can transition the offer to accepted.
4. Persist assignment/status history and outbox event atomically.
5. Return conflict for losing requests; do not report success optimistically.

### Track location
1. Authenticate partner and verify active assignment.
2. Validate coordinates, timestamp, and monotonic sequence/version.
3. Persist durable location history only if needed by product scope; update latest live state in Redis.
4. Publish or emit a tracking update to authorized clients.
5. On reconnect, fetch a fresh snapshot rather than relying on an uninterrupted stream.

## Evolution rule
Only extract a module into a separate service when independent scaling, deployment, ownership, or isolation is a demonstrated requirement. Document the decision in an ADR.
