# Reliability and Failure Scenarios

Reliability work must specify expected behavior, detection, recovery, and evidence.

| Scenario | Expected behavior | Evidence/test |
|---|---|---|
| Two partners accept one offer simultaneously | One succeeds; other gets conflict | Parallel integration test and database invariant |
| Client retries shipment creation | Same operation is not duplicated when idempotency is supported | Retry test with same key |
| Kafka is unavailable during shipment creation | Shipment and outbox commit; publish is delayed, not lost | Stop broker; create; restore; assert event eventually published |
| Relay crashes after Kafka publish | Duplicate publication is possible; consumer effect remains single | Inject crash/window or simulate duplicate event |
| Consumer receives same event repeatedly | Business effect happens once | Replay identical event |
| Redis unavailable | Core shipment reads/writes continue where possible; live state may degrade explicitly | Stop Redis and test fallback |
| Redis loses data | Latest state can be rebuilt or treated as temporarily unavailable | Restart/flush test |
| Backend crashes after DB commit | Committed shipment remains; outbox relay resumes | Kill/restart app integration test |
| Stale location arrives late | Older location does not replace newer accepted location | Out-of-order timestamp/sequence test |
| SSE client disconnects | Client reconnects and fetches snapshot; no permanent loss of current state | Disconnect/reconnect test |
| Tenant context missing/forged | Request is denied | Security integration test |
| Poison Kafka event | Bounded retries, DLQ visibility, no infinite hot loop | Invalid event test |

## Reliability terminology
- **Availability:** proportion of time the service can successfully serve defined requests.
- **Durability:** committed data survives failures covered by the system's guarantees.
- **Idempotency:** repeating an operation has no additional business effect.
- **At-least-once:** an event may be delivered more than once; consumers handle duplicates.
- **Recovery time:** time to restore a defined capability after a failure.
- **Graceful degradation:** some capability is unavailable while core functions continue safely.

## Failure-injection approach
Start with deterministic integration tests using Testcontainers and controlled dependency shutdowns. Later add a repeatable demo script. Avoid random chaos experiments until baseline tests and recovery expectations are stable.
