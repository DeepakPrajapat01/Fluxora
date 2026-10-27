# Domain Model and Invariants

## Core entities

### Tenant
A business/account boundary. Fields: `id`, `name`, `status`, `created_at`.

### User
A login identity. Fields: `id`, `email`, `password_hash` or external identity reference, `status`, timestamps.

### TenantMembership
Associates a user with a tenant and role. Fields: `tenant_id`, `user_id`, `role`, `status`. A user may belong to more than one tenant only if that use case is intentionally supported.

### DeliveryPartnerProfile
Fields: `id`, `tenant_id`, `user_id`, `availability_status`, `vehicle_type` (optional), timestamps.

### Shipment
Fields: `id`, `tenant_id`, `tracking_code`, `customer_id`, origin/destination, parcel metadata, service level, estimated price, `status`, timestamps, optimistic version if used.

### Assignment
Represents an offer or assignment. Fields: `id`, `tenant_id`, `shipment_id`, `partner_id`, `status`, `offered_at`, `accepted_at`, `expires_at`, timestamps.

### ShipmentStatusHistory
Append-oriented record of status transitions: `id`, `tenant_id`, `shipment_id`, `from_status`, `to_status`, `actor_id`, `occurred_at`, `reason`, `correlation_id`.

### LocationUpdate
Latest and/or historical partner location: partner/shipment reference, latitude, longitude, observed time, received time, sequence/version. Define retention and precision deliberately.

### OutboxEvent
Fields: `id`, `aggregate_type`, `aggregate_id`, `event_type`, `schema_version`, `payload`, `occurred_at`, `published_at`, `attempt_count`, `last_error`.

### ProcessedEvent
Consumer deduplication record, commonly unique on `(consumer_name, event_id)`.

## Shipment lifecycle

Initial proposed state machine:

```text
CREATED -> READY_FOR_ASSIGNMENT -> ASSIGNED -> PICKED_UP
        -> IN_TRANSIT -> DELIVERED

Allowed exception paths:
CREATED / READY_FOR_ASSIGNMENT -> CANCELLED
ASSIGNED -> READY_FOR_ASSIGNMENT (if assignment is safely released)
PICKED_UP / IN_TRANSIT -> DELIVERY_FAILED -> (retry or return flow, later)
```

This is a starting proposal. Implement explicit transition validation and revise before coding if a simpler lifecycle is needed. Do not allow arbitrary status updates from clients.

## Assignment lifecycle

`OFFERED -> ACCEPTED -> COMPLETED`

Alternative terminal/exception states: `REJECTED`, `EXPIRED`, `CANCELLED`.

A partner can accept only an eligible, unexpired offer for a shipment that can be assigned. A shipment must not have two active accepted assignments.

## Required invariants

1. Every tenant-owned row is scoped to a tenant.
2. Tracking codes are unique at the intended scope.
3. Only authorized users can view or mutate a shipment.
4. Shipment transitions follow the state machine.
5. At most one active accepted assignment exists for a shipment.
6. An accepted assignment and corresponding shipment state change commit atomically.
7. A committed critical domain change has a corresponding outbox event.
8. Reprocessing an event does not repeat a consumer's business effect.
9. A stale location update cannot replace a newer accepted location.
10. History is append-oriented; current status is not the only evidence of past transitions.

## Database implementation notes

- Add foreign keys, check constraints, and unique constraints for invariants that the database can enforce.
- Consider a partial unique index for one active assignment per shipment if assignment statuses support it; otherwise use a dedicated active-assignment representation or transaction/locking strategy.
- Use UTC timestamps (`timestamptz` in PostgreSQL).
- Use migrations; do not rely on automatic schema generation in shared environments.
- Index actual query paths, then validate with query plans.
