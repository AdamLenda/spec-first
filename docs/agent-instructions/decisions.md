# Decisions

A decision record (ADR) states the **technical why**: one architectural or technical decision and its
reasoning. Digest of `specification/decisions/README.md`. Template:
`specification/decisions/decision-template.md`. Example:
`docs/examples/tideline/specification/decisions/0003-idempotent-webhook-handling.md`.

An accepted decision record is one of the two things that ratify a requirement; the other is a feature file.

## When to write one

Write one when the reason for a change is technical rather than a business capability:

- adopting a pattern or architectural style;
- choosing or replacing a technology;
- a cross-cutting policy such as telemetry, error handling, or authentication approach;
- a constraint taken on deliberately, such as a performance budget.

Do not write one for a choice local to a single story; put that in the story's Implementation Notes.

Never write one to describe what the code already does. That is extraction, and it yields `extracted`
requirements. A decision record needs a human who can say the choice was, or now is, deliberate.

## Create

1. Take the next `ADR-NNNN` from `specification/decisions/decision-index.md`.
2. Copy the template to `specification/decisions/NNNN-kebab-name.md`. The directory is flat.
3. Set `status: proposed`. Add the index row **in the same change**.
4. Fill the sections (below). One decision per record; if the Decision needs "and also", split it.
5. Present it and get explicit acceptance.
6. Set `status: accepted`, fill `decided` and `deciders`, update the index row.
7. Only now may requirements cite it in their `decisions` field. List them under Related Requirements.

If the user declines, set `status: rejected` with the date, and keep the file.

## Metadata

| Field | Value |
|---|---|
| `id` | `ADR-NNNN` from the index |
| `name` | kebab-case, unique |
| `status` | `proposed` → `accepted` → `superseded`, or `proposed` → `rejected` |
| `decided` | ISO date of acceptance or rejection; `—` while proposed |
| `deciders` | Who approved, separated by ` · ` |
| `domains` | `domain:` tags it constrains, or `—` if it applies everywhere |
| `supersedes` | ADR IDs this replaces, or `—` |
| `superseded-by` | ADR ID that replaced this, or `—` |
| `references` | Markdown links separated by ` · `, or `—` |

## Sections and the test for each

| Section | Passes when |
|---|---|
| Context | A reader new to the project sees why this was a question at all. Describes the problem, not the solution |
| Options Considered | At least two realistic options, including doing nothing, each with its advantage and its cost, described fairly |
| Decision | One or two sentences, "We will ...", clear enough to tell whether a given design conforms |
| Consequences | Says what gets easier and what gets harder. At least one cost |
| Assumptions | One `> ⚠️ **Assumption:**` callout per unconfirmed belief, or "None." |
| Related Requirements | `REQ-NNNN · one line` each. Informational; the requirement's `decisions` field is the authoritative link |

Challenge a decision that arrives with a single option. Normative "SHALL" statements do not go here; they are
requirements.

## Change

A `proposed` record is a draft: edit it freely.

An `accepted` record is immutable. Do not touch Context, Options, Decision, or Consequences. To change the
decision:

1. Write a new record whose `supersedes` names the old one. Take it through acceptance.
2. On the old record, set `status: superseded` and `superseded-by`. Update both index rows.
3. Write a requirements amendment: revise every requirement that cited the old record to cite the new one, or
   retire it if the new decision removes the behaviour.

The only other edit allowed on an accepted record is keeping Related Requirements current.

## Gate

A decision record must be `accepted` by a human before any requirement citing it is drafted.
