# Features

A **feature document** describes the business-facing value of a set of changes. It answers *why*: what
problem is being solved, who benefits, and what the outcome looks like from a business or user perspective.
Technical detail belongs in requirements, epics, and stories, not here; technical *reasoning* belongs in a
[decision record](../decisions/README.md).

A feature file is one of the two things that can ratify a requirement. A requirement is `ratified` when it
cites a feature file in its `features` field or an accepted decision record in its `decisions` field; see
[requirement sources](../requirements/README.md#status-values-and-the-three-sources).

## When a feature file exists

A feature file is created when implementation **begins**. Feature files are not roadmap or wish-list items.
If work has not started, there is no feature file. This keeps the `specification/features/` directory an honest description
of what the system does or is actively becoming.

Not every change needs a feature. Foundational or cross-cutting work (authentication hardening, a caching
layer, an observability policy) has no business capability of its own; its requirements cite a decision
record instead.

## Directory layout

```
specification/features/
  README.md                       ← This file
  feature-template.md             ← Copy-paste template
  feature-index.md                ← Authoritative registry of all FEAT-XXXX IDs
  <domain>/
    <feature-name>.md             ← One file per feature
```

- Subdirectories are kebab-case domain names and mirror the `domain:` tags used in requirements. Create them
  on demand.
- File names are kebab-case with **no date prefix**. A feature describes a capability, not an event.

Tideline example:

```
specification/features/
  catalog/
    faceted-search.md               ← FEAT-0001
  digital-delivery/
    license-key-delivery.md         ← FEAT-0002
  fulfillment/
    split-shipments.md              ← FEAT-0003
```

## ID namespace

All features share one global, flat, sequential namespace: `FEAT-0001`, `FEAT-0002`, ... IDs are never reused
and never scoped to a domain. `feature-index.md` is the single authoritative registry; assign the next ID from
it and add a row at the same time. Because the index is a single shared file, two branches that each add a
feature will produce a merge conflict, which is exactly the signal you want (see
[docs/process/concurrent-branches-and-id-collisions.md](../../docs/process/concurrent-branches-and-id-collisions.md)).

## Status values

| Status | Meaning |
|---|---|
| `in-progress` | Implementation is actively underway. The feature is not yet live. |
| `active` | Fully implemented and live. |
| `deprecated` | Being phased out. A replacement exists or a removal decision has been made. |
| `removed` | Removed from the system. The document is retained for audit. |

There is deliberately no `planned` status. If you find yourself wanting one, the work has not started and the
feature file should not exist yet. Keep planned work in an issue or a conversation.

## Metadata fields

| Field | Type | Description |
|---|---|---|
| `id` | `FEAT-XXXX` | Globally unique sequential ID from the feature index |
| `name` | kebab-case | Short machine-readable identifier, unique across the corpus |
| `status` | enum | See above |
| `domain` | string | Matches a `domain:` tag used in requirements |
| `depends-on` | FEAT IDs or `—` | Features that must be active or in-progress for this one to function. Separate with ` · ` |
| `references` | markdown links | Epics, designs, decision records, external docs. Separate with ` · `. `—` if none |

## Sections

| Section | What belongs there | Test for a good entry |
|---|---|---|
| Business Value | The pain point or opportunity, who is affected, the observable improvement | Written from the user's or business's perspective, not the implementation's |
| Description | What the system or a user can do afterwards that it could not before | A few sentences; no technical approach |
| Success Criteria | Checkboxes of observable outcomes | A non-engineer could verify each one by using the product or reading a metric |
| Scope | Explicit **Included** and **Not included** lists | Each exclusion says why: deferred to FEAT-XXXX, out of scope for this release, handled by existing behaviour |
| Assumptions | `> ⚠️ **Assumption:** ...` callouts for anything not yet confirmed | Each one can be revisited and resolved; write "None." if there are none |
| Related Requirements | `REQ-NNNN · one line` entries | Informational only; the authoritative link is the `features` field on the requirement |

## Creating a feature file

1. Take the next `FEAT-XXXX` from `feature-index.md`.
2. Choose or create the domain subdirectory.
3. Copy [`specification/features/feature-template.md`](feature-template.md) to `specification/features/<domain>/<name>.md`.
4. Add a row to `feature-index.md` in the same change.
5. Populate the `features` field on every requirement that implements this feature.

## Updating a feature

Feature files are living documents and are edited in place. When scope, success criteria, or status change,
edit the file and update the index row. Feature files do **not** use the amendment model that requirements
use. A requirement that stops contributing to a feature is retired through a requirements amendment, and the
Related Requirements list here is updated to match.

See the worked example: [`docs/examples/tideline/specification/features/digital-delivery/license-key-delivery.md`](../../docs/examples/tideline/specification/features/digital-delivery/license-key-delivery.md).
