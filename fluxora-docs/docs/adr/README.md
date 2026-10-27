# Architecture Decision Records (ADRs)

Record meaningful architectural choices here. Keep each ADR short and append new records rather than silently rewriting history.

## Suggested template

Create files as `0001-short-title.md`:

```markdown
# ADR-0001: Decision title

- Status: Proposed | Accepted | Superseded
- Date: YYYY-MM-DD

## Context
What problem or constraint requires a decision?

## Decision
What are we choosing?

## Alternatives considered
What other plausible options were considered?

## Consequences
Benefits, costs, risks, and migration implications.

## Validation
How will we test whether this decision works?
```

## Initial decisions to record
- Modular monolith before service extraction.
- PostgreSQL as canonical source of truth.
- Transactional outbox for reliable event publication.
- Assignment concurrency strategy (conditional update, row lock, or equivalent).
- JWT/session and refresh-token strategy.
- Redis role and degradation behavior.
- Kafka partition key and event schema versioning.
- SSE reconnect and snapshot behavior.
