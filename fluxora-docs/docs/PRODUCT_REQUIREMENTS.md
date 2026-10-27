# Product Requirements Document (PRD)

**Status:** Draft v1  
**Product:** Fluxora  
**Audience:** Customers, delivery partners, operations administrators, interview reviewers

## 1. Problem statement

Shipment workflows often involve separate tools for booking, assignment, tracking, and operational investigation. Fluxora brings these workflows into one system and uses them to demonstrate production-oriented backend engineering: consistent state transitions, concurrency safety, resilient asynchronous work, and useful operational visibility.

This is a learning and portfolio project. It is not initially intended to replace a carrier's production transport management system.

## 2. Goals

- Customers can create and track shipments.
- Dispatchers/admins can assign shipments to delivery partners.
- Partners can manage availability and update delivery progress.
- Tracking updates are timely and resistant to stale/out-of-order events.
- Concurrent assignment attempts cannot create conflicting assignments.
- Important domain changes are published reliably through an outbox-backed event flow.
- Tenant data is isolated at API and database-query boundaries.
- Failure behavior and performance can be demonstrated with repeatable tests.
- An optional AI assistant can answer authorized operational questions using narrowly scoped tools.

## 3. Personas

### Customer
Needs to book shipments, understand delivery charges and status, and review shipment history.

### Delivery partner
Needs to set availability, view assigned work, accept/reject eligible assignments, update progress, and share location while on an active delivery.

### Operations administrator
Needs to search and manage shipments, manage partner eligibility, inspect delays, and investigate failures.

### Tenant administrator
Manages users and operational settings for one business account. Cannot access another tenant's data.

## 4. Core user journeys

1. A customer signs up or is invited, signs in, and creates a shipment.
2. The system validates shipment details, calculates a transparent estimate, and creates a shipment in `CREATED`.
3. An authorized dispatcher creates an assignment offer or assigns a partner.
4. Competing accept requests are resolved safely; at most one partner wins.
5. The partner progresses the shipment through permitted states.
6. The customer receives status updates and views the latest known tracking information.
7. An administrator identifies delayed shipments and inspects the shipment event history.
8. Operators can simulate infrastructure failures and inspect recovery metrics.

## 5. Functional requirements

### FR-01 Authentication
Users can authenticate securely. Passwords are hashed using a supported password encoder. Access is role- and tenant-scoped.

### FR-02 Tenant membership
Every business-owned resource belongs to a tenant. Requests must derive the user's tenant membership from trusted server-side identity, not from an untrusted body field alone.

### FR-03 Shipment creation
Create a shipment with origin, destination, parcel metadata, service level, and contact details. Validate required fields and return a stable public tracking reference.

### FR-04 Price estimate
Return an estimate using a documented deterministic formula/configuration. Clearly label it as an estimate; do not imply live carrier pricing.

### FR-05 Shipment status
Expose current status and a chronological status history. Enforce legal state transitions on the server.

### FR-06 Assignment
Authorized users can offer/assign eligible shipments to partners. Concurrent accept attempts must result in no more than one active accepted assignment.

### FR-07 Partner availability
Partners can update availability. Assignment eligibility must be checked server-side.

### FR-08 Tracking
Authorized customers and tenant operators can read shipment tracking. Partners can submit location updates only for appropriate active work. Reject invalid, stale, or out-of-order updates according to the tracking policy.

### FR-09 Event publication
Critical domain changes create durable outbox records in the same database transaction. A publisher relays them to Kafka; consumers tolerate duplicate delivery.

### FR-10 Operational dashboard
Show shipments by status, delayed shipments, partner workload, event processing failures, and key service metrics.

### FR-11 Audit trail
Record relevant state changes and administrative actions with actor, tenant, timestamp, and correlation ID. Avoid logging secrets or unnecessary personal data.

### FR-12 AI operations assistant (later phase)
Answer a small set of operational questions by invoking allow-listed server-side tools. Apply the same authentication, tenant isolation, and data minimization as ordinary API endpoints.

## 6. Non-functional requirements

- **Correctness:** no double acceptance of an assignment; legal shipment transitions only.
- **Durability:** committed critical changes survive process restarts.
- **Idempotency:** retries of supported write operations do not create duplicate business actions.
- **Security:** deny by default; tenant and role checks enforced server-side.
- **Performance:** establish a reproducible baseline before optimization.
- **Observability:** correlate API requests, domain events, and background processing.
- **Maintainability:** modular code, documented APIs, automated tests, reproducible local setup.
- **Recoverability:** documented and tested behavior for dependency outages.

## 7. Success measures

These are targets to validate, not claims to put on a resume before measurement.

- 100% of concurrency tests preserve the single-active-assignment invariant.
- Replaying the same event does not duplicate a consumer's business effect.
- An outbox record is not lost when Kafka is unavailable during a database commit.
- Tenant-isolation tests reject cross-tenant reads and writes.
- API p50/p95/p99 latency, throughput, error rate, and Kafka consumer lag are measured under a documented workload.
- A clean checkout can start the development environment using documented commands.

## 8. Out of scope for initial release

- Real payments, refunds, or invoicing.
- Real carrier integrations and legally binding delivery SLAs.
- Route optimization and live traffic prediction.
- Native mobile apps.
- Multi-region active-active deployment.
- Autonomous AI actions that mutate shipments or assignments.
- Production use with real customer data.

## 9. Open product decisions

- Whether the first demo uses one tenant with tenant-ready data boundaries or multiple demo tenants.
- Which parcel fields are essential for the first booking form.
- Exact delay threshold and business calendar rules.
- Whether assignment is dispatcher-led, partner-claimed, or supports both.
- Which map/location provider, if any, will be used. Start with coordinates and a simple map only if it adds value.
