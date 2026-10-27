# AI Operations Assistant (Stretch Phase)

## Purpose
Allow authorized operators to ask operational questions in natural language, for example:
- “Which shipments in my tenant are delayed?”
- “Summarize the most common delay reasons this week.”
- “Show shipments that have not changed status for more than 24 hours.”

The assistant is a query interface, not an authority for business truth. Answers must be grounded in tool results and include the relevant time window and scope.

## Design
1. Authenticate the user normally.
2. Pass trusted user and tenant context to the assistant service.
3. Expose a small allow-list of read-only tools, such as `find_delayed_shipments` and `summarize_status_counts`.
4. Validate tool arguments against strict schemas.
5. Execute parameterized queries with tenant and permission constraints enforced server-side.
6. Return minimized results to the model.
7. Cite result identifiers or link to the corresponding internal UI when appropriate.
8. Audit tool invocation metadata and errors.

## Prohibited behavior
- No arbitrary SQL generation/execution.
- No unrestricted shell, browser, network, or database tools.
- No cross-tenant search.
- No shipment mutation, assignment, cancellation, or partner management in the first version.
- No disclosure of system prompts, secrets, or other tenants' data.
- No treating shipment notes, customer text, or retrieved documents as trusted instructions.

## Evaluation
Test correct answers, empty results, ambiguous questions, prompt injection in shipment notes, unauthorized users, cross-tenant attempts, malformed tool arguments, provider timeout, and model unavailability.

If the LLM is unavailable, core logistics workflows must continue to work.
