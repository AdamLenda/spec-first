# Requirements Consolidation

## The problem

The requirements corpus is append-only. That property is what makes it auditable and safe to author
concurrently, but it has a cost: every change adds a document, and the set of documents a reader must apply to
know the current state only ever grows. A requirement revised four times exists as five blocks in five files.
For an AI agent doing the pre-flight read, this is context spent on history rather than on the current state.

Consolidation is the periodic compaction step that keeps the corpus readable **without giving up the
append-only model**.

## The idea

A **consolidation document** materialises the current effective state of every requirement into one file. It
is itself just another dated document in the sequence, with `type: consolidation`. After it exists, the read set
for anyone wanting the current state is:

```
latest consolidation  +  amendments created after it, in filename order
```

The folded documents move to `specification/requirements/archive/`. They are not edited and not deleted; git history and the
archive keep the full audit trail.

```
before                                              after
──────                                              ─────
202601120900-baseline-requirements.md       ┐       archive/
202602031430-faceted-search.md              │         202601120900-baseline-requirements.md
202602201015-gift-wrapping.md               ├──▶      202602031430-faceted-search.md
202603101500-license-key-delivery.md        │         202602201015-gift-wrapping.md
202603221100-split-shipments.md             ┘         202603101500-license-key-delivery.md
                                                      202603221100-split-shipments.md
                                                    202604010900-consolidated-requirements.md   ← new read start
202604150800-tax-rounding.md                        202604150800-tax-rounding.md               ← unchanged, still live
```

## Invariants

Consolidation is a **projection**, not an edit. It must hold these invariants, and
the [`consolidate-requirements`](../../.claude/skills/consolidate-requirements/SKILL.md) skill checks them:

1. **No requirement's meaning changes.** The normative text of each active requirement in the consolidation is
   byte-identical to the latest block in the folded documents.
2. **IDs, revisions, and dates are preserved.** `revision` is not reset to 1. `revised` keeps the date of the
   last real change.
3. **Nothing is lost.** Every ID that ever existed appears either as an active block or as a row in the retired
   ledger.
4. **The folded documents are untouched.** They are moved, not modified.
5. **Links still resolve.** References to archived documents elsewhere in the repository are repointed.

If you find yourself wanting to merge two overlapping requirements, reword an awkward one, or finally retire
something obsolete, that is good; write it down in the consolidation report and do it afterwards **as an
ordinary amendment**. Mixing cleanup into consolidation destroys the guarantee that consolidation is safe to
run without review of every line.

## What a consolidation document contains

| Section | Content |
|---|---|
| Metadata | `type: consolidation`, counts, `next-req-id`, `supersedes` listing every folded document |
| Manifest | One row per folded document: ID, type, created, added / revised / retired counts |
| Active requirements | Every `ratified` or `extracted` requirement as a full block, plus a `source` row naming the document that last changed it |
| Retired ledger | One row per retired requirement: ID, name, retired in, retired by, last active revision |
| Assumptions | Carried verbatim with their requirements; never resolved during consolidation |

Status, `features`, and `decisions` are carried verbatim too. Consolidation never promotes an `extracted`
requirement to `ratified`, even when a covering feature or decision now exists; that is a candidate cleanup
for an amendment.

The `source` row is the one addition to the block template. It answers "where did this text come from?" now
that the originating document is in the archive.

Template: [`specification/requirements/templates/consolidation-document-template.md`](../../specification/requirements/templates/consolidation-document-template.md).

## When to consolidate

Choose a trigger and record it in the decisions log. Reasonable options:

- **Count-based:** after every N amendments (five to ten works well for a small team).
- **Size-based:** when the read set exceeds a token or line budget that fits comfortably in an agent's
  context alongside the actual work.
- **Cadence-based:** at each release or at the start of each quarter.
- **On demand:** when a pre-flight read starts to feel slow.

Consolidate on the main branch only, from a clean working tree, with no requirement-bearing branches about to
merge. A consolidation touches many files (archive moves, link updates), so merging an amendment branch across
it is noisy. The simplest rule: consolidation is its own merge request, and open amendment branches rebase
onto it, which usually means moving their link paths and nothing else.

## Procedure summary

1. Parse the read set; for each ID the last block wins.
2. Refuse to proceed on corpus defects (duplicate IDs in one document, non-increasing revisions, dangling
   `parents` or `retired-by`, bad `next-req-id`). Fix those with an amendment first.
3. Write the consolidation document from the template.
4. Move folded documents to `archive/`; add an archive README index.
5. Repoint links across the repository.
6. Verify the invariants; run the validator if one exists.
7. Report counts and candidate cleanups for a follow-up amendment.

The full procedure is the skill itself, run as `/consolidate-requirements`:
[`.claude/skills/consolidate-requirements/SKILL.md`](../../.claude/skills/consolidate-requirements/SKILL.md).

## Reading rules after consolidation

- **Pre-flight** (in the `refine-change` skill): start from the latest consolidation, then apply later
  amendments. The skill already says this.
- **Auditing a requirement's history:** follow its `source` row into the archive, then the earlier documents
  listed in the manifest. Or run `git log -S 'REQ-0121'` across `specification/requirements/`.
- **Consolidating again:** the latest consolidation is the first document in the next read set. Consolidations
  chain; the manifest of each lists what it folded, so the chain is walkable without git.

## Why not just rewrite the baseline?

Rewriting the baseline in place would be simpler, but it would make the baseline a moving target, break every
link that cites the baseline as the origin of a requirement, and lose the property that any document, once
merged, is final. Keeping consolidations as new, dated, append-only documents preserves all three and costs one
extra file per compaction.
