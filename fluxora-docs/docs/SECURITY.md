# Security and Tenant Isolation

## Threat model

Protect customer and partner information, prevent cross-tenant access, restrict operational actions, and ensure the AI assistant cannot bypass existing authorization. Treat all browser input and all LLM output as untrusted.

## Authentication
- Use Spring Security and a well-supported password encoder if passwords are stored locally.
- Keep access tokens short-lived if JWT is chosen; define refresh, revocation, and logout behavior.
- Store secrets in environment variables or a secrets manager, never in Git.
- Configure CORS narrowly for known frontend origins.
- Rate-limit authentication, public tracking, and expensive endpoints.

## Authorization
- Deny by default.
- Enforce role and tenant membership on every protected request.
- A user being authenticated does not mean they can access every shipment.
- Apply object-level authorization for shipment, assignment, partner profile, and admin operations.
- Avoid trusting role, tenant, partner ID, or customer ID from request bodies.

## Tenant isolation
- Resolve tenant context from verified identity/membership and explicit, validated tenant selection where applicable.
- Include tenant scope in repository queries and mutation predicates.
- Add tests that attempt to read, update, cancel, and track another tenant's resources.
- Use tenant-aware unique constraints where identifiers are tenant-scoped.
- Consider PostgreSQL row-level security only as defense in depth after the application-level model is clear.
- Ensure cache keys and Kafka event payloads preserve tenant scope without leaking unnecessary data.

## Data protection
- Minimize personally identifiable information.
- Do not put addresses, phone numbers, JWTs, or secrets in general-purpose logs or Kafka events unless necessary and explicitly protected.
- Define location precision, retention, and access rules.
- Avoid using real customer data in demos and load tests.
- Provide safe error responses.

## AI-specific controls
- Give the model only narrow read-only tools in the first version.
- Authorize every tool call on the server using the authenticated user's tenant and permissions.
- Use parameterized queries and fixed tool schemas.
- Do not let the model construct arbitrary SQL or choose unrestricted URLs/tools.
- Treat shipment notes and other retrieved text as untrusted data, not instructions.
- Redact sensitive fields before returning tool results.
- Log tool name, caller, tenant, and outcome, not hidden reasoning or unnecessary sensitive content.
- Require explicit human confirmation for any future write action.

## Security acceptance checklist
- [ ] Unauthenticated calls to protected endpoints fail.
- [ ] Customer cannot use partner/admin endpoints.
- [ ] Partner cannot update a shipment outside their active assignment.
- [ ] Tenant A cannot read or mutate Tenant B resources.
- [ ] Invalid/stale tokens are rejected.
- [ ] Logs do not expose secrets or sensitive shipment fields.
- [ ] AI tool authorization is independently tested.
