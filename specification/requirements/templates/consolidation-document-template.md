# Consolidated Requirements — <application name>

| Field | Value |
|---|---|
| document-id | `YYYYMMDDHHNN-consolidated-requirements` |
| type | `consolidation` |
| created | YYYY-MM-DD |
| created-by | <name or "consolidate-requirements command, reviewed by <name>"> |
| requirement-count | [N] active · [M] retired |
| next-req-id | REQ-[highest ID + 1] |
| supersedes | `YYYYMMDDHHNN-baseline-requirements` · `YYYYMMDDHHNN-first-amendment` · `YYYYMMDDHHNN-second-amendment` |
| amended-by | — |

This document materialises the current effective state of every requirement defined in the documents listed in
`supersedes`, which have been moved to [`specification/requirements/archive/`](archive/). No requirement's meaning was
changed by this consolidation. To determine the current state of any requirement, read this document and then
apply any amendment documents with a later timestamp, in filename order.

See [`specification/requirements/README.md`](README.md) for the amendment model, template format, and category taxonomy.

---

## Manifest

| Document | Type | Created | Added | Revised | Retired |
|---|---|---|---|---|---|
| `YYYYMMDDHHNN-baseline-requirements` | baseline | YYYY-MM-DD | 238 | — | — |
| `YYYYMMDDHHNN-first-amendment` | amendment | YYYY-MM-DD | 9 | 0 | 0 |
| `YYYYMMDDHHNN-second-amendment` | amendment | YYYY-MM-DD | 4 | 5 | 1 |

---

## Active requirements

<!-- Same section headings as the baseline. Each block is the latest revision, verbatim, plus a `source` row. -->

### 1. <Section name>

---

#### REQ-NNNN · kebab-case-name

| Field | Value |
|---|---|
| name | `kebab-case-name` |
| status | `ratified` |
| categories | `functional` `application` `domain:example` |
| revision | 2 |
| revised | YYYY-MM-DD |
| parents | REQ-NNNN |
| features | FEAT-XXXX |
| decisions | — |
| references | [Label](relative/path) |
| source | `YYYYMMDDHHNN-second-amendment` |

Normative text, byte-identical to the winning block.

> ⚠️ **Assumption:** carried verbatim if present.

---

## Retired requirements

| ID | Name | Retired in | Retired by | Last active revision |
|---|---|---|---|---|
| REQ-NNNN | `kebab-case-name` | `YYYYMMDDHHNN-second-amendment` | REQ-NNNN | 1 |
