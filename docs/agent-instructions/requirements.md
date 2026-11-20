# Requirements

A requirement states **what** the system does, normatively. Digest of `specification/requirements/README.md`. Templates:
`specification/requirements/templates/requirement-block-template.md`, `specification/requirements/templates/amendment-document-template.md`,
`specification/requirements/templates/baseline-document-template.md`. Example: `docs/examples/tideline/specification/requirements/202603101500-license-key-delivery.md`.

## Reading the current state

Latest consolidation (or the baseline if there is none), then each later amendment in filename order. For any
ID the last block wins. Documents in `specification/requirements/archive/` are history, not part of the read set.

## What may be a requirement

Write one only for behaviour that is implemented, or that a refinement conversation has just decided and
tied to a feature or decision.

Do not write one for:

- planned or aspirational work;
- the unimplemented part of a partly built item (document the implemented part only);
- test infrastructure, build tooling, or developer workflow.

## The block

Exact shape. Tools parse it, so do not vary field names, order, or punctuation.

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
| references | [Label](../../src/path/File.ext) |

The system SHALL ... (one behaviour).

> ⚠️ **Assumption:** Only if needed.

---
```

| Field | Rule |
|---|---|
| `name` | kebab-case, unique across the corpus, descriptive |
| `status` | `ratified`, `extracted`, or `retired` |
| `categories` | Backtick tags separated by spaces. At least one type tag **and** at least one `domain:` tag (or `infrastructure` when no application domain applies) |
| `revision` | Starts at 1. Plus one each time an amendment re-emits the block |
| `revised` | ISO date of that revision |
| `parents` | Comma-separated REQ IDs, or `—`. This is the hierarchy; headings are only navigation |
| `features` | FEAT IDs separated by ` · `, or `—` |
| `decisions` | ADR IDs separated by ` · `, or `—`. Only `accepted` records |
| `references` | Markdown links separated by ` · `. Repository files by path relative to the document; external documents by URL |
| `retired-by` | Present only when `status` is `retired`: the superseding REQ ID, or `—`. Goes directly after `status` |

Parsing contract: a block starts at `^#### (REQ-\d{4}) · (.+)$` and ends at the next `---`; table rows match
`^\| (\w[\w-]*) \| (.*) \|$`; the first paragraph after the table is the normative text; a following
`> ⚠️ **Assumption:**` blockquote belongs to the same requirement.

## Status and the three sources

| Source | Status | `features` | `decisions` |
|---|---|---|---|
| A business capability in a feature file | `ratified` | one or more | optional |
| A technical decision in an `accepted` decision record | `ratified` | optional | one or more |
| The existing software, nothing else | `extracted` | `—` | `—` |

- `ratified` if and only if `features` or `decisions` is non-empty. `extracted` if and only if both are `—`.
- Everything a refinement produces is `ratified`. If a drafted requirement has nothing to cite, go back to
  Step 0 and establish its feature or decision.
- Never mark something `ratified` because it is obviously right or because the user agreed in conversation.
- To promote an `extracted` requirement: establish the feature or decision, then re-emit the block in an
  amendment with the reference added, `status: ratified`, and `revision` plus one.
- `retired` blocks keep the `features` and `decisions` they had, and add `retired-by`.
- When a decision is superseded, revise every requirement citing it to cite the replacement, or retire it.

## Tags

Type tags: `feature` (high-level parent), `functional`, `security`, `integration`, `data`, `non-functional`,
`application`, `infrastructure`.

Domain tags come from the code's module structure and are listed in `specification/requirements/README.md`. Use only tags in
that table; add a row when a new top-level module appears. A cross-cutting requirement carries every domain it
touches.

## Writing the text

- RFC 2119 keywords only: **SHALL** (mandatory), **SHOULD** (recommended; deviation needs a documented
  reason), **MAY** (optional).
- One behaviour per requirement. If "and" joins two independently testable behaviours, split them.
- Be specific. A `feature` requirement is the parent of several detailed `functional` children:

  > REQ-0120 · digital-delivery-feature · `feature` `application` `domain:digital-delivery`
  > The system SHALL deliver purchased digital products to the buying customer without manual intervention.

  > REQ-0121 · download-link-expiry · `functional` `security` `application` `domain:digital-delivery` · parents: REQ-0120
  > The system SHALL issue download links that expire 72 hours after issuance and SHALL reject requests
  > presenting an expired link with HTTP 410.

- State what is assumed in a callout directly after the block.

## Writing an amendment

1. Create `specification/requirements/YYYYMMDDHHNN-<kebab-description>.md` from the amendment template.
2. Sections, in order: **New requirements**, **Revised requirements**, **Retired requirements**.
3. **Add:** full blocks, IDs continuing the global sequence ([id-allocation.md](id-allocation.md)).
4. **Revise:** re-emit the complete block with `revision` plus one and `revised` updated. The block must stand
   alone; a reader never diffs it against the earlier version. Add a dated revision note in the intro saying
   why.
5. **Retire:** re-emit the complete block with `status: retired`, `retired-by`, `revision` plus one, and the
   original normative text kept.
6. Fill the metadata table last:

   | Field | Value |
   |---|---|
   | `document-id` | The filename without extension |
   | `type` | `amendment` |
   | `created` / `created-by` | Date and author. Say so when an AI produced it |
   | `requirement-count` | `N new · M revised · K retired` |
   | `next-req-id` | First unused ID after this document |
   | `supersedes` | `—` (consolidations only) |
   | `amended-by` | `—` always; it cannot be maintained under the append-only rule |

7. Intro paragraph: what the change is, which feature or decision it rests on, a link to that file.

## Check before presenting

- [ ] Every block matches the shape exactly
- [ ] Every block has a type tag and a domain tag (or `infrastructure`)
- [ ] No ID reused; new IDs sequential with no gaps
- [ ] Every `parents`, `retired-by`, `features`, and `decisions` reference resolves
- [ ] Every `ratified` block cites a feature or an `accepted` decision; every `extracted` block cites neither
- [ ] Revised and retired blocks carry a higher `revision` than their previous block
- [ ] `requirement-count` and `next-req-id` are correct
- [ ] No earlier requirements document was touched
