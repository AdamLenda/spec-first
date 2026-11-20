# Consolidation

Compacting the append-only corpus into one document so the pre-flight read stays short. Digest of
`docs/process/requirements-consolidation.md`. The executable procedure is the `consolidate-requirements` skill
(`.claude/skills/consolidate-requirements/SKILL.md`); run that rather than improvising.

## The one rule

Consolidation is a **projection, not an edit**. It changes no requirement's meaning.

If you see requirements that should be merged, split, reworded, or retired, list them in the report. They are
handled afterwards by an ordinary amendment, never during consolidation.

## Preconditions

- On the main branch, with a clean working tree.
- No requirement-bearing branch about to merge.
- It will be its own merge request. Open amendment branches rebase onto it afterwards.
- The repository's trigger has been reached (see the decisions log), or a human asked for it.
- `specification/requirements/templates/consolidation-document-template.md` is present.

## Invariants to hold and verify

1. Normative text of every active requirement is byte-identical to its latest block.
2. `revision`, `revised`, `status`, `features`, and `decisions` are preserved. Nothing resets to 1 and nothing
   is promoted from `extracted` to `ratified`.
3. Every ID that ever existed appears exactly once: as an active block or as a row in the retired ledger.
4. Folded documents are moved to `specification/requirements/archive/`, never modified or deleted.
5. Links to archived documents elsewhere in the repository are repointed. Link text and REQ IDs stay as they
   were.
6. Assumption callouts are carried verbatim, never resolved.

## Procedure

1. **Build the effective state.** Parse the read set (latest consolidation or baseline, plus later
   amendments, in filename order). For each ID the last block wins. Record its source document.
2. **Stop on corpus defects**, writing nothing: an ID emitted twice in one document; a non-increasing
   `revision`; `retired` without `retired-by`; a dangling `parents` or `retired-by`; an unexplained ID gap; a
   `next-req-id` lower than an ID the document contains. These are fixed by an amendment first.
3. **Write** `specification/requirements/YYYYMMDDHHNN-consolidated-requirements.md` from the template:
   - metadata with `type: consolidation`, `requirement-count` as `N active · M retired`, and `supersedes`
     listing every folded document, oldest first;
   - a manifest row per folded document (type, created, added, revised, retired);
   - every `ratified` or `extracted` requirement as its winning block, under the baseline's section headings,
     plus one added `source` row naming the document that last changed it;
   - a retired ledger: ID, name, retired in, retired by, last active revision.
4. **Archive.** Move the folded documents to `specification/requirements/archive/` with names unchanged. Add or update
   `specification/requirements/archive/README.md`, one line per document pointing at the consolidation.
5. **Repoint.** Update the directory listing in `specification/requirements/README.md`. Grep features, epics, stories,
   decisions, and agent instruction files for links to the archived documents and fix the paths.
6. **Verify** the invariants by re-parsing the result. Run the validator if there is one. Confirm no file
   outside `specification/requirements/` changed except for link paths.
7. **Report:** documents folded, active and retired counts, size before and after, candidate cleanups, and
   anything uncertain.

An optional argument names a cut-off document; later documents stay live and out of this consolidation.

## Candidate cleanups to look for and report

- near-duplicate or contradictory requirements;
- `references` paths that no longer exist;
- `extracted` requirements that an existing feature or decision appears to cover and could be ratified;
- requirements citing a superseded decision;
- parents that point at retired requirements;
- over-broad requirements that should be split.

Apply none of them.

## Afterwards

- Current state is read from the latest consolidation plus later amendments.
- To audit one requirement's history, follow its `source` row into the archive and the manifest, or run
  `git log -S 'REQ-0121' -- specification/requirements/`.
- The next consolidation starts from this one. Consolidations chain through their manifests.
- Never rewrite the baseline in place as a shortcut.
