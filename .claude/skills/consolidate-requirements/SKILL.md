---
name: consolidate-requirements
description: Compact the append-only requirements corpus into a single consolidation document without changing any requirement's meaning, and archive the documents folded into it. Use only when the user asks to consolidate or compact the requirements corpus.
argument-hint: "[cut-off document ID]"
---

# Consolidate Requirements

You are performing a **semantics-preserving compaction** of the `specification/requirements/` corpus. The corpus is append-only:
a baseline plus dated amendments, each of which may add, revise (re-emit with a higher revision), or retire
requirements. Over time the number of documents a reader must apply grows. Your job is to materialise the current
effective state of every requirement into one new document, so that future readers start there.

**You are not allowed to change what any requirement says.** Consolidation is a projection, not an edit. If you
notice requirements that should be merged, split, reworded, or retired, write them down in the report at the end;
they are handled afterwards by an ordinary amendment, never by this skill.

Read `docs/process/requirements-consolidation.md` if you need the rationale.

## Inputs

- `specification/requirements/README.md`, for the template, taxonomy, and status vocabulary.
- The current **read set**: the most recent document with `type: consolidation` if one exists, otherwise the
  baseline; plus every amendment created after it, in filename order.
- Optional argument: `$ARGUMENTS` may name a cut-off document ID. Documents after the cut-off are left out of
  this consolidation and remain live.

## Procedure

### 1. Build the effective state

Parse every requirement block in the read set, in document order, then in order of appearance within each
document. For each `REQ-NNNN`, the **last block seen wins**; it carries the highest `revision`. Record for each
ID:

- the winning block, verbatim (fields, normative text, assumption callouts);
- the document ID in which the winning block appears (its `source`);
- the full list of document IDs in which any block for this ID appeared (its `history`).

Stop and report, without writing anything, if you find:

- the same ID emitted twice in one document;
- a later block whose `revision` is not greater than the earlier one;
- a block with `status: retired` lacking `retired-by`;
- a `parents` or `retired-by` reference to an ID that does not exist anywhere in the read set;
- an ID gap in the sequence that is not explained by a known skip;
- a `next-req-id` in any document that is lower than an ID it contains.

These are corpus defects. They must be fixed by an amendment before consolidation can proceed.

### 2. Produce the consolidation document

Create `specification/requirements/YYYYMMDDHHNN-consolidated-requirements.md` from
`specification/requirements/templates/consolidation-document-template.md`:

**Metadata.** `type: consolidation`. `requirement-count` as `N active · M retired`. `next-req-id` equal to the
highest ID present plus one. `supersedes` lists every document ID folded in, oldest first.

**Manifest.** A table of the folded documents: document ID, type, created date, requirements added, revised,
retired. This replaces the informational `amended-by` field the amendment model could never maintain.

**Active requirements.** Every requirement whose effective status is `ratified` or `extracted`, as its winning
block, verbatim, grouped under the same section headings the baseline used (or by domain tag if the baseline had
none). Add one row to each block's table:

```
| source | `YYYYMMDDHHNN-document-id` |
```

so readers can trace the block back to the document that last changed it. Keep `revision` and `revised` as they
were; do not reset them.

**Retired requirements.** A compact ledger, not full blocks:

```
| ID | Name | Retired in | Retired by | Last active revision |
|---|---|---|---|---|
| REQ-0057 | permanent-download-link | 202603101500-license-key-delivery | REQ-0121 | 1 |
```

The full retired text remains in the archived documents and in git history. If your team's audit requirements
demand full blocks for retired requirements, keep them in an appendix instead; say which you did in the report.

**Assumptions.** Carry every `> ⚠️ **Assumption:**` callout with its requirement. Do not resolve or delete any.

### 3. Archive the folded documents

Move every document listed in `supersedes` into `specification/requirements/archive/`, preserving file names. Add or update
`specification/requirements/archive/README.md` with one line per archived document pointing at the consolidation that
superseded it. Do not edit the archived documents themselves.

### 4. Update pointers

- In `specification/requirements/README.md`, update the directory listing so the consolidation document is shown as the current
  starting point for reads.
- Grep the repository for links to the archived documents (features, decisions, epics, stories, agent instruction
  files) and update their paths to `specification/requirements/archive/...`. Do not change the link text or the REQ IDs.

### 5. Verify

Re-parse the consolidation document and confirm:

- [ ] Every active ID in the read set appears exactly once in the consolidation.
- [ ] Every retired ID in the read set appears exactly once in the ledger.
- [ ] For each ID, the consolidated normative text is byte-identical to the winning block's text.
- [ ] Every `parents` and `retired-by` reference resolves within the consolidation.
- [ ] `requirement-count` and `next-req-id` are correct.
- [ ] No file outside `specification/requirements/` changed except link paths.
- [ ] The repository's requirements validator (if present) passes.

### 6. Report

Finish with a short report:

1. Documents folded, counts of active and retired requirements, resulting file size versus the read set's total.
2. **Candidate cleanups observed** (for a future amendment, not for this skill): near-duplicate requirements,
   requirements whose `references` paths no longer exist, `extracted` requirements that an existing feature or
   decision record appears to cover and could be ratified, requirements citing a superseded decision, parents that point at retired requirements, over-broad requirements that should be split.
3. Anything you were unsure about.

Do not apply any of the candidate cleanups.
