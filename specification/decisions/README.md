# Decisions

An **architecture decision record** (ADR) captures one technical decision and the reasoning behind it: the
adoption of a design pattern, a choice of technology, a stance on telemetry and observability, a security
posture. It answers the technology-facing *why*, the way a [feature](../features/README.md) answers the
business-facing *why*.

## Decisions are one of two things that ratify a requirement

Every requirement has exactly one of three sources:

| Source | `status` | The requirement cites |
|---|---|---|
| A business capability | `ratified` | A feature file, in its `features` field |
| A technical decision | `ratified` | An accepted decision record, in its `decisions` field |
| The existing software | `extracted` | Neither. It was inferred from code or configuration |

A requirement may cite both a feature and a decision. A requirement that cites neither is `extracted`, however
confident anyone is about it. Writing the missing decision record, and then revising the requirement through
an amendment to cite it, is how an `extracted` requirement becomes `ratified`.

## What deserves a decision record

Write one when the answer to "why is it built this way?" is not a business capability:

- adopting a pattern or architectural style (layering, event sourcing, idempotent handlers);
- choosing or replacing a technology (database, queue, framework, hosting);
- a cross-cutting policy (logging and tracing conventions, error handling, authentication approach);
- a constraint taken on deliberately (a performance budget, a compatibility guarantee).

Do not write one for a choice local to a single story. That belongs in the story's Implementation Notes.

One decision per record. If the Decision section needs "and also", write two records.

## Directory layout

```
specification/decisions/
  README.md                       ← This file
  decision-template.md            ← Copy-paste template
  decision-index.md               ← Authoritative registry of all ADR-NNNN IDs
  NNNN-kebab-case-name.md         ← One file per decision
```

- File names are the four-digit number from the ID, then a kebab-case name: `0003-idempotent-webhook-handling.md`.
- The directory is flat. Decisions are usually cross-cutting, so they are not grouped by domain; the `domains`
  field records what they touch.

## ID namespace

All decisions share one global, flat, sequential namespace: `ADR-0001`, `ADR-0002`, ... IDs are never reused.
`decision-index.md` is the single authoritative registry; assign the next ID from it and add a row in the same
change. Because the index is one shared file, two branches that each add a decision conflict at merge, which
is the intended signal (see
[concurrent branches](../../docs/process/concurrent-branches-and-id-collisions.md)).

## Status values

| Status | Meaning |
|---|---|
| `proposed` | Drafted and under discussion. Requirements may not cite it yet. |
| `accepted` | Approved by a human. In force. Requirements may cite it. |
| `superseded` | Replaced by a later decision, named in `superseded-by`. Retained for audit. |
| `rejected` | Considered and declined. Retained so the reasoning is not repeated. |

## Metadata fields

| Field | Type | Description |
|---|---|---|
| `id` | `ADR-NNNN` | Globally unique sequential ID from the decision index |
| `name` | kebab-case | Short machine-readable identifier, unique across the corpus |
| `status` | enum | See above |
| `decided` | ISO date or `—` | The date the decision was accepted or rejected. `—` while `proposed` |
| `deciders` | names or handles | Who approved it. Separate with ` · ` |
| `domains` | `domain:` tags or `—` | The domains the decision constrains. `—` if it applies everywhere |
| `supersedes` | ADR IDs or `—` | Earlier decisions this one replaces |
| `superseded-by` | ADR ID or `—` | Set when a later decision replaces this one |
| `references` | markdown links | Designs, benchmarks, external docs, epics. Separate with ` · `. `—` if none |

## Sections

| Section | What belongs there | Test for a good entry |
|---|---|---|
| Context | The forces at play: the problem, the constraints, what makes a decision necessary now | A reader new to the project understands why this was a question at all |
| Options Considered | Each realistic alternative with its main advantage and cost, including "do nothing" | At least two options; the rejected ones are described fairly |
| Decision | The choice, stated in one or two sentences in the active voice: "We will ..." | Unambiguous enough that someone could tell whether a given design conforms |
| Consequences | What becomes easier, what becomes harder, what is now ruled out, what must be revisited later | Includes at least one cost |
| Assumptions | `> ⚠️ **Assumption:** ...` callouts for anything not yet confirmed | "None." if there are none |
| Related Requirements | `REQ-NNNN · one line` entries | Informational only; the authoritative link is the `decisions` field on the requirement |

A decision record states the choice and its reasons. The normative statements that follow from it
("The system SHALL ...") are requirements, and are written in the requirements corpus.

## Creating a decision record

1. Take the next `ADR-NNNN` from `decision-index.md`.
2. Copy [`decision-template.md`](decision-template.md) to `NNNN-kebab-case-name.md` with `status: proposed`.
3. Add a row to `decision-index.md` in the same change.
4. Get explicit human approval. Set `status: accepted`, fill `decided` and `deciders`, update the index row.
5. Populate the `decisions` field on every requirement that follows from it.

## Changing a decision

An accepted decision record is **immutable**. Its Context, Options, Decision, and Consequences are never
rewritten, because requirements were ratified against that text.

The only edits permitted after acceptance are:

- `status` and `superseded-by`, when a later decision replaces it;
- the Related Requirements list, which is informational;
- the index row, to match.

To change a decision, write a new record whose `supersedes` names the old one, and set the old one to
`superseded`. Then write a requirements amendment: every requirement that cited the old decision is revised to
cite the new one, or retired if the new decision removes the behaviour.

A `proposed` record is a draft and is edited freely until it is accepted or rejected.

See the worked example:
[`docs/examples/tideline/specification/decisions/0003-idempotent-webhook-handling.md`](../../docs/examples/tideline/specification/decisions/0003-idempotent-webhook-handling.md).
