# Decision Title

| Field | Value |
|---|---|
| id | `ADR-NNNN` |
| name | `kebab-case-name` |
| status | `proposed` |
| decided | — |
| deciders | — |
| domains | `domain:example` |
| supersedes | — |
| superseded-by | — |
| references | [Design](https://example.org/design) · [Epic](../epics/YYYY.MM.DD-epic-name/README.md) |

## Context

<!--
The forces that make a decision necessary. Describe the problem and the constraints, not the solution.

  - What is the situation today, and what is wrong or missing?
  - What constraints apply (scale, team, existing technology, compliance, cost)?
  - Why does this need deciding now?

Example: "The payment provider delivers each notification at least once and retries on any non-2xx response.
Three handlers currently process notifications, and each guards against duplicates differently, one of them
with a read-then-write check that is unsafe under concurrency."
-->

## Options Considered

<!--
Each realistic alternative, including doing nothing. For each: what it is, its main advantage, its main cost.
Describe the rejected options fairly; a future reader should see why they were tempting.
-->

1. **Option name.** ...
2. **Option name.** ...

## Decision

<!--
The choice, in one or two sentences, active voice. One decision per record.

Example: "We will make every handler of an externally delivered notification idempotent, and enforce that
with a database unique constraint on the notification's natural key rather than with application-level
checks."
-->

We will ...

## Consequences

<!--
What follows from the decision, good and bad. Include at least one cost.

  - What becomes easier or safer?
  - What becomes harder, slower, or ruled out?
  - What must be revisited if an assumption changes?
-->

- ...
- ...

## Assumptions

<!--
Anything the decision rests on that is not yet confirmed. Write "None." if there are none.
-->

> ⚠️ **Assumption:** ...

## Related Requirements

<!--
REQ-NNNN IDs that follow from this decision. Informational: the authoritative link is the `decisions` field
on each requirement. This list may be updated after the record is accepted.
-->

- REQ-XXXX · one-line description
