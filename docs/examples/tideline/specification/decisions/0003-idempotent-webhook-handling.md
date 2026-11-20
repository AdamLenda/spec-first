# Idempotent Webhook Handling

| Field | Value |
|---|---|
| id | `ADR-0003` |
| name | `idempotent-webhook-handling` |
| status | `accepted` |
| decided | 2026-03-05 |
| deciders | A. Example · B. Example |
| domains | `domain:payments` `domain:orders` `domain:digital-delivery` |
| supersedes | — |
| superseded-by | — |
| references | [Epic](../epics/2026.03.10-license-key-delivery/README.md) |

## Context

The payment provider delivers each notification at least once and retries on any non-2xx response, so the same
"payment succeeded" event routinely arrives two or three times, sometimes concurrently. Tideline already has
two handlers that react to it, and each guards against duplicates differently: one checks a processed-events
table, the other reads the order status before writing. The second is a read-then-write check and has let a
duplicate through under load.

License key delivery adds a third handler whose side effect, consuming a key from a seller's pool, cannot be
undone cheaply. A consistent rule is needed before it is written.

## Options Considered

1. **Leave it to each handler.** No new work, and each handler stays free to choose. Cost: the existing
   inconsistency continues and the next handler repeats the unsafe pattern.
2. **A shared processed-events table checked by middleware.** One place to reason about. Cost: the check and
   the handler's own writes are in separate transactions unless every handler is restructured, so a crash
   between them either drops or repeats an event.
3. **Idempotency by database unique constraint on each handler's natural key.** The database rejects the
   duplicate write atomically, in the same transaction as the side effect. Cost: every handler must identify a
   natural key and its table needs the constraint.

## Decision

We will make every handler of an externally delivered notification idempotent, and enforce that with a
database unique constraint on the natural key of the record the handler creates, rather than with
application-level checks.

## Consequences

- A duplicate notification is absorbed by the database and the handler returns success; no duplicate side
  effects under concurrency.
- Handlers use insert-or-return-existing in one transaction instead of select-then-insert.
- Every new handler needs a natural key. A handler with none must introduce one before it can ship.
- The two existing handlers do not conform and need follow-up work, tracked separately.
- Integration tests for handlers must run against a real database; a mocked repository cannot exercise the
  constraint.

## Assumptions

> ⚠️ **Assumption:** The provider's notification payload always carries enough to derive the natural key
> (order ID and line item). Verified for payment events; not yet checked for refund events.

## Related Requirements

- REQ-0122 · entitlement record created per digital line item, idempotent against repeated notifications
