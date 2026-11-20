# Amendment: License Key Delivery

| Field | Value |
|---|---|
| document-id | `202603101500-license-key-delivery` |
| type | `amendment` |
| created | 2026-03-10 |
| created-by | A. Example |
| requirement-count | 5 new · 0 revised · 1 retired |
| next-req-id | REQ-0125 |
| supersedes | — |
| amended-by | — |

This amendment introduces requirements for the **License Key Delivery** feature (`FEAT-0002`), which replaces
permanent emailed download links with expiring per-customer links backed by an entitlement record, and adds
license-key assignment for licensed products. All new requirements have `status: ratified`: each cites the
feature, and REQ-0122 also cites the decision record `ADR-0003` that governs its idempotency. REQ-0057 (permanent download links) is retired and superseded by REQ-0121.

See [`specification/features/digital-delivery/license-key-delivery.md`](../features/digital-delivery/license-key-delivery.md)
for the business-facing description.

---

## New requirements

---

#### REQ-0120 · digital-delivery-feature

| Field | Value |
|---|---|
| name | `digital-delivery-feature` |
| status | `ratified` |
| categories | `feature` `application` `domain:digital-delivery` |
| revision | 1 |
| revised | 2026-03-10 |
| parents | REQ-0001 |
| features | FEAT-0002 |
| decisions | — |
| references | [specification/features/digital-delivery/license-key-delivery.md](../features/digital-delivery/license-key-delivery.md) |

The system SHALL deliver purchased digital products to the buying customer without manual intervention, and
SHALL allow the customer to recover access to any digital purchase from their account for as long as the
entitlement remains active.

---

#### REQ-0121 · download-link-expiry

| Field | Value |
|---|---|
| name | `download-link-expiry` |
| status | `ratified` |
| categories | `functional` `security` `application` `domain:digital-delivery` |
| revision | 1 |
| revised | 2026-03-10 |
| parents | REQ-0120 |
| features | FEAT-0002 |
| decisions | — |
| references | [DownloadLinkService.ts](../../src/digital-delivery/DownloadLinkService.ts) |

The system SHALL issue download links that expire 72 hours after issuance, SHALL bind each link to a single
entitlement, and SHALL respond to a request presenting an expired or unknown link with HTTP 410.

---

#### REQ-0122 · entitlement-per-digital-line-item

| Field | Value |
|---|---|
| name | `entitlement-per-digital-line-item` |
| status | `ratified` |
| categories | `functional` `data` `application` `domain:digital-delivery` `domain:orders` |
| revision | 1 |
| revised | 2026-03-10 |
| parents | REQ-0120 |
| features | FEAT-0002 |
| decisions | ADR-0003 |
| references | [EntitlementRepository.ts](../../src/digital-delivery/EntitlementRepository.ts) |

The system SHALL create exactly one entitlement record for each digital line item when an order transitions to
`paid`, recording the customer, the order line, the product asset, and the creation time. Entitlement creation
SHALL be idempotent with respect to repeated payment notifications for the same order.

---

#### REQ-0123 · license-key-assignment

| Field | Value |
|---|---|
| name | `license-key-assignment` |
| status | `ratified` |
| categories | `functional` `data` `application` `domain:digital-delivery` |
| revision | 1 |
| revised | 2026-03-10 |
| parents | REQ-0122 |
| features | FEAT-0002 |
| decisions | — |
| references | [LicenseKeyPoolService.ts](../../src/digital-delivery/LicenseKeyPoolService.ts) |

For a product flagged as licensed, the system SHALL assign one unused key from the seller's uploaded pool to
the entitlement at creation time, SHALL mark that key as consumed in the same transaction, and SHALL NOT assign
the same key to more than one entitlement.

> ⚠️ **Assumption:** Pool exhaustion at assignment time is treated as a delivery failure to be surfaced to the
> seller, not as a checkout failure for the customer. The customer-facing behaviour in this case has not been
> specified.

---

#### REQ-0124 · license-pool-low-warning

| Field | Value |
|---|---|
| name | `license-pool-low-warning` |
| status | `ratified` |
| categories | `functional` `non-functional` `application` `domain:digital-delivery` `domain:notifications` |
| revision | 1 |
| revised | 2026-03-10 |
| parents | REQ-0123 |
| features | FEAT-0002 |
| decisions | — |
| references | [LicensePoolMonitorJob.ts](../../src/jobs/LicensePoolMonitorJob.ts) |

The system SHALL notify the seller when the count of unused keys in a pool falls below a per-pool threshold,
and SHOULD send at most one such notification per pool per 24 hours.

---

## Retired requirements

---

#### REQ-0057 · permanent-download-link

| Field | Value |
|---|---|
| name | `permanent-download-link` |
| status | `retired` |
| retired-by | REQ-0121 |
| categories | `functional` `application` `domain:digital-delivery` |
| revision | 2 |
| revised | 2026-03-10 |
| parents | REQ-0001 |
| features | — |
| decisions | — |
| references | [DownloadLinkService.ts](../../src/digital-delivery/DownloadLinkService.ts) |

The system SHALL email the customer a permanent download link for each digital product at the time of purchase.

---
