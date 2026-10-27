# Event-Driven Design and Kafka Reliability

## Why events?
Events decouple follow-up work from the transaction that changes business state. Examples include `ShipmentCreated`, `AssignmentAccepted`, `ShipmentStatusChanged`, and `DeliveryCompleted`.

Kafka is not the source of truth for shipment state. PostgreSQL remains authoritative.

## Event envelope

Proposed fields:
- `eventId`: stable UUID generated once and reused on retries.
- `eventType`: versioned event name.
- `schemaVersion`: integer.
- `occurredAt`: UTC timestamp.
- `tenantId`: required for scoped processing.
- `aggregateType` and `aggregateId`: e.g. `Shipment` and shipment ID.
- `correlationId` / `causationId`: tracing and causal relationship.
- `payload`: minimal domain data needed by consumers.

Avoid including secrets or unnecessary personal data.

## Transactional outbox flow

1. A command validates and changes domain rows inside a PostgreSQL transaction.
2. The same transaction inserts an outbox row.
3. A relay reads unpublished rows and publishes to Kafka.
4. After broker acknowledgement, the relay marks the row published.
5. If the relay crashes after publishing but before marking the row, the event may publish again.
6. Consumers deduplicate by event ID and commit their processed-event record with their local business effect.

This provides reliable at-least-once publication/processing patterns; do not describe it as end-to-end exactly-once delivery.

## Consumer idempotency
- Store `(consumer_name, event_id)` under a unique constraint.
- Check/insert the deduplication record in the same transaction as the consumer's database effect.
- If already processed, acknowledge without repeating the effect.
- For external side effects, use that provider's idempotency mechanism or a second outbox/work queue.

## Ordering and partitioning
Use shipment ID as a Kafka key when events for a shipment must remain ordered within one partition. Consumers must still validate event versions/timestamps because retries and downstream processing can create practical ordering challenges.

## Retry and dead-letter handling
- Retry transient failures with bounded backoff.
- Classify permanent schema/validation errors separately.
- After configured attempts, route poison messages to a dead-letter topic with safe diagnostic metadata.
- Expose retry count, oldest unpublished outbox age, consumer lag, and DLQ volume.
- Document a replay process; replay must remain safe because consumers are idempotent.

## Initial event catalogue
- `ShipmentCreated.v1`
- `ShipmentCancelled.v1`
- `AssignmentOffered.v1`
- `AssignmentAccepted.v1`
- `AssignmentRejected.v1`
- `ShipmentStatusChanged.v1`
- `PartnerLocationUpdated.v1` (only if event volume and consumers justify it)

Do not publish every high-frequency location update to Kafka by default. First define expected volume and whether downstream consumers need the full stream.
