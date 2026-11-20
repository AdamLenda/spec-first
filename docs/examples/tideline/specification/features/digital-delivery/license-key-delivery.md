# License Key Delivery

| Field | Value |
|---|---|
| id | `FEAT-0002` |
| name | `license-key-delivery` |
| status | `in-progress` |
| domain | `digital-delivery` |
| depends-on | — |
| references | [Epic](../../epics/2026.03.10-license-key-delivery/README.md) |

## Business Value

Digital products are currently delivered as a single permanent download link sent by email at purchase time.
Links are routinely forwarded, so sellers see downloads far exceeding sales, and customers who lose the email
have no way to recover their purchase except through support. Sellers of software products additionally need
to hand each buyer a unique license key, which Tideline cannot do today, so those sellers list elsewhere.

This feature makes digital delivery per-customer and recoverable: links expire, purchases are always
re-downloadable from the customer's account, and software sellers can have Tideline issue a license key with
each order.

## Description

After purchase, a customer receives a download link that works for 72 hours. At any time afterwards they can
request a fresh link from their order history. For products the seller has flagged as licensed, Tideline
draws a key from the seller's uploaded pool and shows it on the order confirmation, in the confirmation email,
and in the account area. Every digital purchase is recorded as an entitlement, which is the single source of
truth for "does this customer own this item".

## Success Criteria

- [ ] A customer can re-download a purchased file from their account without contacting support
- [ ] A download link stops working 72 hours after it was issued
- [ ] A customer buying a licensed product sees their license key on the confirmation page and in the email
- [ ] A seller is warned when their license key pool drops below a configurable threshold
- [ ] Support tickets tagged "lost download link" fall by at least half within one month of launch

## Scope

**Included:**
- Entitlement record per digital line item
- Expiring, per-customer download links with re-issue from the account area
- License key pools uploaded by sellers and assigned one per entitlement
- Confirmation page, email, and account area display of keys and links

**Not included:**
- Generating license keys on the seller's behalf (sellers upload pools; generation deferred to a future feature)
- Download bandwidth metering or rate limiting beyond link expiry (handled by existing CDN behaviour)
- Transferring an entitlement between customer accounts (out of scope for this release)

## Assumptions

> ⚠️ **Assumption:** Every digital product has exactly one downloadable asset. Multi-file products have not been
> specified and may require a follow-up decision.

> ⚠️ **Assumption:** License key pools are small enough (tens of thousands of keys) to store as rows rather than
> as uploaded files. Confirmed informally with two sellers; not yet verified against the largest seller.

## Related Requirements

- REQ-0120 · digital delivery feature (parent)
- REQ-0121 · download links expire after 72 hours
- REQ-0122 · entitlement record created per digital line item
- REQ-0123 · license key assigned from seller pool at entitlement creation
- REQ-0124 · low-pool warning to seller
