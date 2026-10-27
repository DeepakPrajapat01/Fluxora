# Observability and Performance

## Signals

### Metrics
Track request count, latency histogram, error count, JVM health, database connection pool usage, outbox backlog/oldest age, Kafka consumer lag, retry/DLQ count, SSE connections, and location update acceptance/rejection counts.

Use low-cardinality labels. Never label metrics with user IDs, shipment IDs, tracking codes, email addresses, or raw URLs.

### Logs
Use structured logs with timestamp, level, service, environment, correlation ID, route template, outcome, and safe error classification. Avoid logging tokens, passwords, full addresses, or entire event payloads.

### Traces
If time permits, add distributed tracing across HTTP requests, database operations, outbox publishing, and Kafka consumption. Propagate correlation/trace context deliberately.

## Initial dashboard panels
- API request rate and error rate
- p50/p95/p99 latency by route template
- JVM heap and garbage collection
- Database pool active/idle/waiting
- Outbox pending count and oldest pending age
- Kafka consumer lag by consumer group
- Retry and dead-letter counts
- Location update rate and stale-update rejection rate
- SSE active connections

## Performance workflow
1. Define an observable symptom and target.
2. Establish a reproducible baseline.
3. Profile the bottleneck.
4. Change one major variable.
5. Repeat the same workload.
6. Compare percentiles, throughput, errors, and resource utilization.
7. Record the result and limitations.

Redis, caching, indexes, batching, and concurrency changes should be justified by measurements rather than added speculatively.
