---

#### Story 01: add-entitlement-table-and-repository

| Field | Value |
|---|---|
| name | `add-entitlement-table-and-repository` |
| description | Add the `entitlements` table, its domain model, and a repository with create-if-absent semantics; no callers yet |
| lifecycle | `completed` |
| story-points | 2 |
| references | [OrderRepository.ts](../../../src/orders/OrderRepository.ts) · [V041__create_orders.sql](../../../db/migrations/V041__create_orders.sql) · [OrderRepository.test.ts](../../../test/orders/OrderRepository.test.ts) |

---

## Acceptance Criteria

- [x] Migration `V058__create_entitlements.sql` creates `entitlements` with columns `id`, `customer_id`,
  `order_line_id`, `asset_id`, `license_key_id` (nullable), `created_at`, and a unique constraint on `order_line_id`
- [x] `Entitlement` domain model exists in `src/digital-delivery/Entitlement.ts`
- [x] `EntitlementRepository.createIfAbsent(orderLineId, customerId, assetId)` inserts a row or returns the
  existing one for that `order_line_id` without error (REQ-0122 idempotency)
- [x] `EntitlementRepository.findByCustomer(customerId)` returns entitlements newest first
- [x] Integration tests cover create, duplicate create, and find-by-customer against a real database
- [x] No production code path calls the repository yet; both feature flags remain off
- [x] All existing tests pass; lint passes

## Implementation Notes

**Migration.** Copy the structure of `V041__create_orders.sql`. Use the same `id` type and `created_at`
default as the orders table. The unique constraint on `order_line_id` is what makes `createIfAbsent` safe
under concurrent payment notifications; do not rely on application-level checks alone.

**Repository.** Follow `OrderRepository.ts` for the constructor-injection and query patterns. `createIfAbsent`
should use an insert with `ON CONFLICT (order_line_id) DO NOTHING` followed by a select, in one transaction,
rather than select-then-insert.

**Model.** `license_key_id` is nullable now and populated in Story 05. Keep it in this migration so Story 05
does not need a second schema change.

**Tests.** Mirror `OrderRepository.test.ts`: real database container, no mocks. The duplicate-create test
should call `createIfAbsent` twice with the same `order_line_id` and assert a single row.

**Nothing is wired.** This story is a pure addition and is safe to merge independently with both flags off.

## Files Modified

- `db/migrations/V058__create_entitlements.sql` (new)
- `src/digital-delivery/Entitlement.ts` (new)
- `src/digital-delivery/EntitlementRepository.ts` (new)
- `test/digital-delivery/EntitlementRepository.test.ts` (new)
