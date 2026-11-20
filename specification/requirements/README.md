# Requirements

The `specification/requirements/` directory is the authoritative, normative description of what the system does. It is
structured as a **baseline + amendment model**, borrowed from the way RFC errata work: a single dated baseline
document is written once, and every subsequent change is a dated amendment document that references
requirement IDs. Nothing already written is ever edited.

## Directory layout

```
specification/requirements/
  README.md                                   ← Explains this directory, the template, taxonomy, and decisions log
  YYYYMMDDHHNN-baseline-requirements.md       ← Baseline: the permanent revision-0 record
  YYYYMMDDHHNN-<description>.md               ← Amendments, in chronological filename order
  YYYYMMDDHHNN-consolidated-requirements.md   ← Optional periodic consolidation (see docs/process/requirements-consolidation.md)
  archive/                                    ← Documents folded into a consolidation
  templates/                                  ← Requirement block and document templates (not part of the corpus)
```

File names begin with a `YYYYMMDDHHNN` timestamp followed by a short kebab-case description. The timestamp
gives a total ordering, which is what makes "apply amendments in filename order" work.

## The amendment model

To know the **current state** of any requirement, read the baseline and then apply each amendment in filename
order. This is analogous to reading a git log.

Amendment documents:

- **Re-emit the full requirement block** for any requirement they modify, with `revision` incremented and
  `revised` updated. An amendment is therefore self-contained: a reader never needs to diff it against an
  earlier document.
- **May add** new requirements, continuing the global `REQ-NNNN` sequence.
- **May retire** requirements by re-emitting the block with `status: retired` and a `retired-by` field that
  points at the superseding requirement (or `—` if simply removed).
- **Never modify** the baseline or any earlier amendment. Those documents are permanent.

The model trades a little reading effort for complete, auditable history and for safe concurrent authoring:
because each change is a new file, two people (or two agents) rarely edit the same lines.

## ID namespace

All requirements share one global, flat, sequential namespace: `REQ-0001`, `REQ-0002`, ... IDs are never
reused and never scoped to a document, feature, or domain. Structure comes from category tags and `parents`
links, not from the ID.

Each document records the `next-req-id` after its own additions. New authoring starts from the
`next-req-id` of the most recent document. For what happens when two branches both take the same next ID, see
[docs/process/concurrent-branches-and-id-collisions.md](../../docs/process/concurrent-branches-and-id-collisions.md).

## The requirement block

Every requirement is a Markdown section in this exact shape. The field table is designed to be trivially
machine-parseable into YAML.

```markdown
---

#### REQ-NNNN · kebab-case-name

| Field | Value |
|---|---|
| name | `kebab-case-name` |
| status | `ratified` |
| categories | `functional` `application` `domain:digital-delivery` |
| revision | 1 |
| revised | YYYY-MM-DD |
| parents | REQ-NNNN |
| features | FEAT-XXXX |
| decisions | — |
| references | [DownloadLinkService.ts](../../src/digital-delivery/DownloadLinkService.ts) |

Normative requirement text using RFC 2119 SHALL / SHOULD / MAY language.

---
```

### Field definitions

| Field | Type | Description |
|---|---|---|
| `name` | kebab-case | Short machine-readable identifier, unique across the corpus. Descriptive, not generic. |
| `status` | enum | `ratified`, `extracted`, or `retired` (below) |
| `categories` | space-separated backtick tags | At least one **type tag** and at least one **domain tag** (or `infrastructure` when no application domain applies) |
| `revision` | integer | Starts at 1; incremented each time an amendment re-emits the block |
| `revised` | ISO date | Date of the most recent revision |
| `parents` | REQ IDs or `—` | Comma-separated parent requirement IDs. The authoritative hierarchy mechanism |
| `features` | FEAT IDs or `—` | The feature files whose business value this requirement supports, ` · ` separated |
| `decisions` | ADR IDs or `—` | The accepted decision records this requirement follows from, ` · ` separated |
| `references` | markdown links | `[label](relative/path-or-url)`, separated by ` · `. Repository paths are relative to the document, so the links work when rendered (`../../src/...` from this directory) |
| `retired-by` | REQ ID or `—` | Only present when `status` is `retired`. Placed directly after `status` |

### Status values and the three sources

A requirement's status says where it came from. There are exactly three sources:

| Source | Status | `features` | `decisions` |
|---|---|---|---|
| A business capability, described in a [feature file](../features/README.md) | `ratified` | one or more | optional |
| A technical decision, recorded in an accepted [decision record](../decisions/README.md) | `ratified` | optional | one or more |
| The existing software, with no supporting feature or decision | `extracted` | `—` | `—` |

| Status | Meaning |
|---|---|
| `ratified` | Adopted as an explicit decision. The requirement cites at least one feature file or accepted decision record, which is where the reasoning is written down. |
| `extracted` | Inferred from existing implementation (source, configuration, migrations). No feature or decision supports it, so nobody has confirmed it is intended. May warrant stakeholder review. |
| `retired` | Superseded or removed. The block is retained for audit with a `retired-by` field, and keeps the `features` and `decisions` it had. |

The rule is mechanical, so a tool can check it:

- `ratified` **if and only if** `features` or `decisions` is non-empty;
- `extracted` **if and only if** both are `—`.

Consequences:

- **A refinement conversation always produces `ratified` requirements,** because it establishes the feature
  or decision first. A change with no business capability behind it needs a decision record.
- **Confidence is not ratification.** A behaviour everyone agrees is correct is still `extracted` until a
  feature or decision is written and the requirement is revised to cite it.
- **Promotion is an amendment.** To ratify an `extracted` requirement, write (or identify) the feature or
  decision, then re-emit the block with the reference added, `status: ratified`, and `revision` incremented.
- **A superseded decision does not ratify.** When a decision record is superseded, an amendment revises each
  requirement that cited it to cite the replacement, or retires it.

### Requirement language

Use RFC 2119 keywords consistently:

- **SHALL** — mandatory. The system is non-conforming without it.
- **SHOULD** — recommended. Deviations need a documented reason.
- **MAY** — optional.

One behaviour per requirement. If you write "and" between two independently testable behaviours, split them.

### Assumptions

When a statement rests on an assumption not evidenced by source material, add an inline callout immediately
after the block:

```markdown
> ⚠️ **Assumption:** Describe the assumption and what evidence is missing.
```

## Category taxonomy

Every requirement carries one or more **type tags** and one or more **domain tags**.

### Type tags (un-prefixed)

| Tag | Meaning |
|---|---|
| `feature` | High-level feature requirement; parent of more specific requirements |
| `functional` | Specific behaviour the system SHALL or SHOULD exhibit |
| `security` | Authentication, authorization, data protection, threat mitigation |
| `integration` | External service interface requirement |
| `data` | Persistence, schema, caching, data model |
| `non-functional` | Performance, scalability, reliability, observability |
| `application` | Scoped to the application layer (code) |
| `infrastructure` | Scoped to or impacting hosting, orchestration, networking, deployment |

### Domain tags (prefixed `domain:`)

Domain tags are **derived from the code's own package or module structure**, so that every tag maps to a place
in the repository and traceability is direct. Define the table once in this file and extend it when a new top-level module appears.

**This repository's table** is empty: `src/` holds no modules yet. The first refinement that introduces a
module adds its row here.

| Tag | Maps to |
|---|---|
| — | — |

For illustration, the table of the fictional Tideline store used in the
[worked example](../../docs/examples/tideline/README.md):

| Tag | Maps to |
|---|---|
| `domain:catalog` | `src/catalog/`, product listing, browsing, search |
| `domain:cart` | `src/cart/` |
| `domain:checkout` | `src/checkout/`, order placement flow |
| `domain:payments` | `src/payments/`, payment provider integration, refunds |
| `domain:orders` | `src/orders/`, order records and status |
| `domain:fulfillment` | `src/fulfillment/`, physical shipping, carrier integration |
| `domain:digital-delivery` | `src/digital-delivery/`, download links, license keys, entitlements |
| `domain:customers` | `src/customers/`, accounts and profiles |
| `domain:auth` | `src/auth/`, authentication and authorization |
| `domain:promotions` | `src/promotions/`, coupons and discounts |
| `domain:tax` | `src/tax/`, tax calculation |
| `domain:notifications` | `src/notifications/`, email and SMS |
| `domain:reporting` | `src/reporting/` |
| `domain:jobs` | `src/jobs/`, background scheduling |
| `domain:admin` | `src/admin/`, back-office tooling |

A cross-cutting requirement carries every domain tag it touches.

## Granularity rules

1. **Bias toward specificity.** A high-level `feature` requirement is the parent of several detailed
   `functional` requirements.
2. **Hierarchy lives in `parents`,** not in heading nesting or document sections.
3. **One behaviour per requirement.**
4. **Cross-cutting requirements get multiple domain tags.**

Too coarse:

> The system SHALL deliver digital products, including issuing download links, generating license keys, and
> recording entitlements.

Correct, high-level feature:

> REQ-0120 · digital-delivery-feature · `feature` `application` `domain:digital-delivery`
> The system SHALL deliver purchased digital products to the buying customer without manual intervention.

Correct, detailed child:

> REQ-0121 · download-link-expiry · `functional` `security` `application` `domain:digital-delivery` · parents: REQ-0120
> The system SHALL issue download links that expire 72 hours after issuance and SHALL reject requests
> presenting an expired link with HTTP 410.

## What is not a requirement

Do not create requirements for:

1. **Planned or aspirational work.** No ID is assigned until implementation begins or a refinement
   conversation ratifies the decision.
2. **Partially implemented items.** Document only the implemented portion.
3. **Test infrastructure**, **build tooling**, and **developer workflow** (git conventions, lint rules).

## Document metadata

Every requirements document, baseline or amendment, opens with a metadata table. See
[`specification/requirements/templates/baseline-document-template.md`](templates/baseline-document-template.md) and
[`specification/requirements/templates/amendment-document-template.md`](templates/amendment-document-template.md).

| Field | Meaning |
|---|---|
| `document-id` | The filename without extension |
| `type` | `baseline`, `amendment`, or `consolidation` |
| `created` / `created-by` | Provenance. For AI-produced baselines, say so |
| `requirement-count` | Baseline and consolidation: `N active · M retired`. Amendment: `N new · M revised · K retired` |
| `next-req-id` | The first unused ID after this document |
| `supersedes` | Documents this one replaces for reading purposes (consolidations only); otherwise `—` |
| `amended-by` | Left as `—` at creation. The amendment model forbids editing earlier documents, so this field is informational and is maintained only in a consolidation document's manifest |

## Decisions log

The structural decisions made when the corpus was established, recorded so future maintainers do not
relitigate them. This repository takes the process defaults:

| Decision | Choice |
|---|---|
| Directory location | `specification/` at the repo root, holding `features/`, `decisions/`, `requirements/`, `epics/` |
| File structure | One baseline + dated amendments in one directory |
| ID format | Flat sequential `REQ-NNNN`, single global namespace |
| Status vocabulary | `ratified`, `extracted`, `retired` |
| Requirement sources | Three: a feature, a decision record, or the existing software. `ratified` requires citing one of the first two |
| Category tags | Type tags + `domain:` tags combined in one field |
| Domain tag source | The code's package structure |
| Requirement language | RFC 2119 |
| Amendment style | Re-emit the full block with incremented revision |
| Infrastructure scope | Included, tagged `infrastructure` |
| Granularity | Specific; `feature` parents over `functional` children |
| Features and decisions | Linked through the `features` and `decisions` fields; a feature file exists only once work begins, and only an `accepted` decision may be cited |

Not yet decided, to be settled before the first epic and recorded here: feature flag platform and naming
convention, consolidation trigger, and who may move an epic from `draft` to `ready`
(see [adopting the process](../../docs/process/adopting-in-a-new-repo.md)).

## Machine readability

Because every block starts with `#### REQ-NNNN` and uses the same two-column table, a small parser can turn
the whole corpus into YAML or JSON. This is what makes validation, consolidation, and ID renumbering tractable.

```yaml
- id: REQ-0121
  name: download-link-expiry
  status: ratified
  categories: [functional, security, application, "domain:digital-delivery"]
  revision: 1
  revised: "2026-03-01"
  parents: [REQ-0120]
  features: [FEAT-0002]
  decisions: []
  references:
    - label: DownloadLinkService.ts
      url: src/digital-delivery/DownloadLinkService.ts
  description: >
    The system SHALL issue download links that expire 72 hours after issuance and SHALL reject
    requests presenting an expired link with HTTP 410.
```

Parsing rules a tool can rely on:

- A requirement block begins at a line matching `^#### (REQ-\d{4}) · (.+)$` and ends at the next `---` rule.
- The metadata table rows match `^\| (\w[\w-]*) \| (.*) \|$`.
- `status` is `ratified` exactly when `features` or `decisions` holds an ID.
- The first non-empty paragraph after the table is the normative text.
- A following blockquote beginning `> ⚠️ **Assumption:**` belongs to the same requirement.

See the worked example: [`docs/examples/tideline/specification/requirements/`](../../docs/examples/tideline/specification/requirements/).
