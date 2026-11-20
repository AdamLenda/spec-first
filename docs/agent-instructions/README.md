# Agent Instructions: Specification-First Process

Operational rules for an agent working in a repository that uses this process. Each file is a digest of one
guide: what to do and in what order. The guides hold the rationale. A person new to the process should read
[`../getting-started.md`](../getting-started.md) instead.

## Pick the file for the task

| You are about to | Read | Guide behind it |
|---|---|---|
| Discuss, scope, or plan any change | The [`refine-change`](../../.claude/skills/refine-change/SKILL.md) skill | `docs/process/end-to-end-workflow.md` Part 2 |
| Create or update a feature file | [features.md](features.md) | `specification/features/README.md` |
| Record or supersede a technical decision | [decisions.md](decisions.md) | `specification/decisions/README.md` |
| Write, revise, or retire a requirement | [requirements.md](requirements.md) and [id-allocation.md](id-allocation.md) | `specification/requirements/README.md`, `docs/process/concurrent-branches-and-id-collisions.md` |
| Plan an epic, write a story, or implement one | [epics-and-stories.md](epics-and-stories.md) | `specification/epics/README.md` |
| Compact the requirements corpus | [consolidation.md](consolidation.md) | `docs/process/requirements-consolidation.md`; run the [`consolidate-requirements`](../../.claude/skills/consolidate-requirements/SKILL.md) skill |
| Extract a baseline or set the process up in a repository | [bootstrap-and-adoption.md](bootstrap-and-adoption.md) | `docs/process/adopting-in-a-new-repo.md`, `docs/process/end-to-end-workflow.md` Part 1; run the [`extract-requirements`](../../.claude/skills/extract-requirements/SKILL.md) skill |

## Rules that always apply

1. **Never edit a merged requirements document.** Baseline, amendments, and consolidations are permanent. A
   change of any size is a new amendment. The only permitted operation on an old document is moving it to
   `specification/requirements/archive/` during consolidation.
2. **IDs are permanent.** `REQ-NNNN` and `FEAT-XXXX` are never changed after merge, never reused, and never
   scoped to a domain or document.
3. **Stop at every gate.** Three approvals are required, each an explicit yes from a human:
   feature file or decision record → requirements; requirements → epic planning; epic `draft` → `ready` → implementation.
   Silence, enthusiasm, or a change of subject is not approval.
4. **Status follows from the source.** A requirement is `ratified` if and only if it cites a feature file
   (`features`) or an `accepted` decision record (`decisions`). It is `extracted` if and only if it cites
   neither. Agreement, confidence, or a conversation does not ratify anything; a written feature or decision
   does.
5. **Nothing planned gets an artifact.** No feature file, no requirement ID, until the work begins. Ideas go
   in an issue or the conversation.
6. **Flag what is assumed.** Anything not evidenced gets `> ⚠️ **Assumption:** ...` directly after the
   statement it qualifies. Do not resolve or delete someone else's assumption without a decision.
7. **Accepted decisions are immutable.** To change one, write a superseding record, then amend the
   requirements that cited the old one.
8. **Close the audit trail.** A story is not complete until Files Modified is filled from `git diff
   --name-only` and committed with the code.

## Which documents change how

| Artifact | Mutability | How to change it |
|---|---|---|
| `specification/requirements/*.md` | Append-only | New amendment that re-emits the full block |
| `specification/features/**/*.md`, `feature-index.md` | Living | Edit in place; keep file and index row in step |
| `specification/epics/**` | Point-in-time | Edit in place; record what was actually done |
| `specification/decisions/*.md` | Immutable once `accepted` | New record that supersedes the old; only `status`, `superseded-by`, and Related Requirements change on the old one |

## Naming and formats

| Thing | Format | Example |
|---|---|---|
| Requirement ID | `REQ-` + 4 digits, global sequence | `REQ-0121` |
| Feature ID | `FEAT-` + 4 digits, global sequence | `FEAT-0002` |
| Requirements document | `YYYYMMDDHHNN-kebab-description.md` (`NN` = minutes) | `202603101500-license-key-delivery.md` |
| Baseline / consolidation | `…-baseline-requirements.md` / `…-consolidated-requirements.md` | `202601120900-baseline-requirements.md` |
| Feature file | `specification/features/<domain>/<kebab-name>.md`, no date | `specification/features/digital-delivery/license-key-delivery.md` |
| Epic folder | `specification/epics/YYYY.MM.DD-kebab-name/` | `specification/epics/2026.03.10-license-key-delivery/` |
| Story file | `NN[letter]-kebab-name.md` | `01-add-entitlement-table.md`, `01a-…` |
| Decision ID | `ADR-` + 4 digits, global sequence | `ADR-0003` |
| Decision record | `specification/decisions/NNNN-kebab-name.md` | `specification/decisions/0003-idempotent-webhook-handling.md` |
| List separator inside a table cell | ` · ` (`parents` uses commas) | `REQ-0120 · REQ-0121` |
| Empty value | `—` | |
| Dates inside documents | ISO `YYYY-MM-DD` | `2026-03-10` |

Requirements documents use a compact timestamp; epic folders use a dotted date. Do not mix them.

## Where everything is

| Path | Holds |
|---|---|
| `specification/features/` | Feature files by domain, `feature-template.md`, `feature-index.md` |
| `specification/requirements/` | The append-only corpus; `templates/` for the block and the baseline, amendment, and consolidation documents; the domain tag table and decisions log in `README.md` |
| `specification/epics/` | One folder per epic, `epic-readme-template.md`, `story-template.md` |
| `specification/decisions/` | Decision records, `decision-template.md`, `decision-index.md` |
| `.claude/skills/` | The executable procedures: `refine-change`, `extract-requirements`, `consolidate-requirements` |
| `src/` | The software the specification describes |
| `docs/process/` | Process guides that span more than one directory |
| `docs/examples/tideline/` | Worked example for a fictional store; illustration only, never part of the corpus |

Requirement `references` and story `references` link to code by path relative to the document:
`../../src/...` from a requirements document, `../../../src/...` from a story.
