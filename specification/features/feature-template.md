# Feature Name

| Field | Value |
|---|---|
| id | `FEAT-XXXX` |
| name | `kebab-case-name` |
| status | `in-progress` |
| domain | `domain-name` |
| depends-on | FEAT-XXXX · FEAT-XXXX |
| references | [Design](https://example.org/design) · [Epic](../../epics/YYYY.MM.DD-epic-name/README.md) |

## Business Value

<!--
Describe the problem this feature solves and who benefits. Write from the perspective of the business or the
end user, not the implementation. A good entry answers:

  - What pain point, gap, or opportunity does this address?
  - Who is affected (customers, partners, internal operators)?
  - What is the measurable or observable improvement once this feature is live?

Example: "Customers who buy a downloadable product currently receive a permanent link by email, which is
routinely shared. This feature issues expiring, per-customer download links, protecting sellers' revenue
while keeping purchases accessible from the customer's account."
-->

## Description

<!--
Summarise what this feature does in plain language. Focus on the capability, not the technical approach.

  - What can a user or the system do after this feature exists that it could not do before?
  - What does "done" look like from a user or business perspective?
  - If this changes existing behaviour, what is changing and why?

A few sentences or a short paragraph. Technical details belong in epics and stories.
-->

## Success Criteria

<!--
Observable, business-facing outcomes. Not technical acceptance criteria (those live in stories).
Ask: "Could a non-engineer confirm this is true by using the product or reviewing a metric?"

Example:
  - [ ] A customer can re-download a purchased file from their account page without contacting support
  - [ ] A download link stops working 72 hours after it was issued
  - [ ] Support tickets about "lost download links" fall measurably within one month of launch
-->

- [ ] ...
- [ ] ...

## Scope

<!--
Explicit boundaries prevent scope creep and show reviewers what is intentionally deferred.

Included: the capabilities, user flows, or behaviours that ARE part of this feature.
Not included: related capabilities that are explicitly out of scope, each with a brief reason
("deferred to FEAT-XXXX", "out of scope for this release", "handled by existing behaviour").
-->

**Included:**
- ...

**Not included:**
- ...

## Assumptions

<!--
Anything assumed during planning that is not yet confirmed by implementation or an explicit stakeholder
decision. Flag each so it can be revisited. Write "None." if there are none.

Example:
  > ⚠️ **Assumption:** Every digital product has exactly one downloadable asset. Multi-file products have
  > not been specified and may need a follow-up decision.
-->

> ⚠️ **Assumption:** ...

## Related Requirements

<!--
REQ-NNNN IDs that implement this feature. Informational: the authoritative link is the `features` field on
each requirement. Keep this list current as requirements are added or retired.
-->

- REQ-XXXX · one-line description
- REQ-XXXX · one-line description
