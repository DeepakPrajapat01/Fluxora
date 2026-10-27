# Testing Strategy

## Test layers

### Unit tests
Test domain rules and pure functions: state transitions, pricing calculation, assignment eligibility, stale-location detection, and event mapping.

### Spring integration tests
Test HTTP validation, security filters, transaction behavior, repository queries, and API response contracts.

### Testcontainers tests
Run realistic PostgreSQL, Kafka, and Redis dependencies in integration tests. Use them for transaction, unique constraint, outbox, consumer, and cache behavior.

### Contract tests
Validate API schemas and event schemas. Keep event schema changes backward compatible where practical; document versioning rules.

### End-to-end tests
Exercise the main customer-to-delivery journey through the API and UI.

### Load tests
Use k6 to exercise documented scenarios with controlled concurrency and test data. Record hardware, container limits, versions, warm-up, duration, and request mix.

## Minimum high-value tests
- Shipment lifecycle permits valid transitions and rejects invalid ones.
- A customer cannot access another customer's or tenant's shipment.
- Many concurrent assignment accepts yield exactly one winner.
- Transaction rollback leaves neither a partial business update nor an orphaned outbox event.
- Kafka outage does not lose committed outbox events.
- Duplicate event processing does not duplicate effects.
- Out-of-order location does not overwrite newer state.
- API errors do not leak stack traces or secrets.
- Redis outage has the documented degradation behavior.

## Load-test report format
For each run record:
- Git commit and build version
- Environment and dependency versions
- CPU/memory limits
- Scenario and request distribution
- Virtual users/concurrency and duration
- Throughput (requests/second)
- p50, p95, p99 latency
- Error rate and timeout count
- Database pool saturation and slow queries
- Kafka consumer lag and outbox backlog, if applicable
- Bottleneck hypothesis and next experiment

Never publish “X% faster” without a comparable baseline, repeated runs, and equivalent conditions.
