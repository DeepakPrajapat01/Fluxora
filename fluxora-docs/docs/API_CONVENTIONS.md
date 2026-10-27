# API Conventions

## General rules

- Base path: `/api/v1`.
- Use JSON for request/response bodies.
- Use UUIDs or another opaque stable identifier for internal resource IDs.
- Use a separate human-shareable tracking code; do not expose database implementation details.
- Use UTC timestamps in ISO 8601 format.
- Validate request bodies and query parameters.
- Paginate collection endpoints; establish a default and maximum page size.
- Document endpoints with OpenAPI.
- Use a correlation/request ID in responses and logs.
- Never trust a tenant ID or role supplied by the browser without server-side verification.

## Initial endpoint map (proposal)

### Identity and tenant
- `POST /api/v1/auth/register` (if self-registration is enabled)
- `POST /api/v1/auth/login`
- `POST /api/v1/auth/refresh` (only if refresh-token flow is selected)
- `GET /api/v1/me`
- `GET /api/v1/tenants/{tenantId}/members` (tenant-admin only)

### Shipments
- `POST /api/v1/shipments`
- `GET /api/v1/shipments`
- `GET /api/v1/shipments/{shipmentId}`
- `GET /api/v1/shipments/{shipmentId}/history`
- `GET /api/v1/tracking/{trackingCode}` (public fields only; rate-limit and avoid exposing PII)
- `POST /api/v1/shipments/{shipmentId}/cancel`

### Dispatch and partner
- `GET /api/v1/partner/assignments`
- `PATCH /api/v1/partner/availability`
- `POST /api/v1/assignments/{assignmentId}/accept`
- `POST /api/v1/assignments/{assignmentId}/reject`
- `POST /api/v1/assignments/{assignmentId}/status` (or a dedicated shipment transition endpoint)

### Tracking
- `POST /api/v1/partner/location`
- `GET /api/v1/shipments/{shipmentId}/tracking`
- `GET /api/v1/shipments/{shipmentId}/tracking/stream` (SSE)

### Operations
- `GET /api/v1/admin/overview`
- `GET /api/v1/admin/shipments/delayed`
- `GET /api/v1/admin/events/failures` (if safely exposed to operators)

The exact routes can change during implementation; document breaking changes.

## HTTP status conventions

- `200 OK`: successful read or update with representation.
- `201 Created`: resource created.
- `202 Accepted`: asynchronous work accepted but not yet completed.
- `204 No Content`: successful operation with no response body.
- `400 Bad Request`: malformed input or validation failure.
- `401 Unauthorized`: missing/invalid authentication.
- `403 Forbidden`: authenticated but not permitted.
- `404 Not Found`: missing resource or deliberately concealed cross-tenant resource.
- `409 Conflict`: conflicting state, duplicate idempotency key, or assignment already claimed.
- `422 Unprocessable Content`: syntactically valid request that fails a domain rule, if used consistently.
- `429 Too Many Requests`: rate limit exceeded.
- `503 Service Unavailable`: required dependency unavailable and operation cannot safely proceed.

## Error response shape

Use a consistent structure, preferably based on RFC 9457 Problem Details, with fields such as `type`, `title`, `status`, `detail`, `instance`, `correlationId`, and optional field validation errors. Never include stack traces, SQL, secrets, or internal infrastructure details.

## Idempotency

For retry-sensitive create/command endpoints, consider an `Idempotency-Key`. Scope keys by tenant and operation; persist the key and result safely. Define retention and behavior for a repeated key with a different payload. Do not apply idempotency keys as a substitute for database uniqueness or transaction correctness.
