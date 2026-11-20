---

#### Epic: license-key-delivery

| Field | Value |
|---|---|
| name | `license-key-delivery` |
| refined-by | A. Example |
| lifecycle | `in-progress` |
| parent-requirements | REQ-0120 · REQ-0121 · REQ-0122 · REQ-0123 · REQ-0124 |
| features | FEAT-0002 |
| decisions | ADR-0003 |
| feature-flags | `digital-delivery.expiring-links` · `digital-delivery.license-keys` |
| references | [Feature](../../features/digital-delivery/license-key-delivery.md) · [Amendment](../../requirements/202603101500-license-key-delivery.md) · [Decision](../../decisions/0003-idempotent-webhook-handling.md) |

---

## Goal

Replace permanent emailed download links with entitlement-backed, expiring, re-issuable links, and assign
seller-supplied license keys to licensed products at purchase time.

## Scope

**Included:**
- `entitlements` and `license_keys` tables and repositories
- Entitlement creation on the `paid` order transition, idempotent against repeated payment notifications
- Expiring download links (72 h) with HTTP 410 on expiry and re-issue from the account area
- License key assignment from a seller pool, transactional with entitlement creation
- Low-pool seller notification job
- Confirmation page, email template, and account area changes

**Not included:**
- Key generation on the seller's behalf
- Entitlement transfer between accounts
- Changes to the carrier or physical fulfilment flow

## Constraints

- `digital-delivery.expiring-links` defaults **off**; while off, REQ-0057 behaviour (permanent link) remains
  live. Removal criteria: 30 days after enablement with no re-issue failures and support ticket volume meeting
  the feature's success criterion.
- `digital-delivery.license-keys` defaults **off**; independent of the first flag so expiring links can ship
  first. Removal criteria: all licensed sellers migrated to pools.
- Stories 01–03 are additive and safe to merge with both flags off. Story 04 onward changes behaviour behind
  the flags.
- Entitlement creation hooks the existing order state machine; it must not change the state machine's
  transitions.

## Stories

- [01 — add-entitlement-table-and-repository](01-add-entitlement-table-and-repository.md)
- [02 — add-license-key-pool-table-and-repository](02-add-license-key-pool-table-and-repository.md)
- [03 — create-entitlements-on-paid-transition](03-create-entitlements-on-paid-transition.md)
- [04 — issue-expiring-download-links-behind-flag](04-issue-expiring-download-links-behind-flag.md)
- [05 — assign-license-keys-behind-flag](05-assign-license-keys-behind-flag.md)
- [06 — account-area-reissue-and-key-display](06-account-area-reissue-and-key-display.md)
- [07 — license-pool-monitor-job](07-license-pool-monitor-job.md)
