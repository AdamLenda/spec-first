# Concurrent Branches and ID Collisions

## The problem, precisely

Two branches each run the refinement loop. Both read `next-req-id: REQ-0300` in pre-flight. Both author an
amendment that defines `REQ-0300`, `REQ-0301`, ... in **different files**. Git merges both cleanly, because
nothing textual overlaps. The corpus now has two unrelated requirements with the same ID, and nothing noticed.

Three distinct things are tangled here and are worth separating:

| Concern | What goes wrong | What solves it |
|---|---|---|
| **Allocation collision** | Two branches mint the same ID | An allocation mechanism that is unique by construction, or one that makes the collision impossible to merge silently |
| **Silent merge** | The collision passes review and CI | A shared file that must change on every allocation, so git conflicts; plus a validator in CI |
| **Semantic duplication** | Two branches state the *same* requirement under *different* IDs | No ID scheme fixes this. The Step 1 gate's conflict check and periodic consolidation review catch it |

The timestamp idea addresses only the first row. It is worth looking at how other append-only, numbered,
branch-authored artifacts have dealt with the same tension, because it is a well-worn problem.

## Prior art

Database migration tooling has lived with exactly this problem for twenty years:

- **Timestamped IDs** (Rails migrations, `20260315142501_add_license_keys.rb`). Second-resolution UTC
  timestamps. Collisions are rare and are resolved by hand at merge time. Ordering is by time of authoring,
  which is usually, but not always, the order you want to apply them in.
- **Sequential IDs with merge detection** (Django migrations). Plain integers, but each migration names its
  parent, so two branches each adding `0007` produce two leaves in the graph. The tool refuses to run until you
  create an explicit merge migration. The conflict is detected structurally, not by git.
- **Composite IDs** (Liquibase changesets). Identity is the triple `(id, author, file)`. No central counter;
  uniqueness comes from the author or file being part of the key.
- **Out-of-order tolerance** (Flyway `outOfOrder`). Keep numeric versions but allow a lower number to arrive
  after a higher one has been applied. Trades strict ordering for merge friendliness.

The lesson across all four: either make the identifier unique without coordination (timestamp, composite), or
keep it coordinated and make the collision **loud** (a structural check that fails). Nobody relies on humans
noticing.

## Assessing the millisecond-timestamp idea

`REQ-1773854123456` is unique enough in practice: two authors would need to mint an ID in the same millisecond.
Its weaknesses are not about uniqueness:

- **Unreadable and unmemorable.** Thirteen digits defeat the point of a short handle that people say aloud and
  type into story files.
- **Sorts by authoring time, not by relationship.** The sequence `0120, 0121, 0122` currently tells a reader
  "these were drafted together". Timestamps scatter them.
- **Over-precise for the actual risk.** The amendment filename already has a minute-resolution timestamp, and
  two amendments in the same minute would collide on *filename* first, which git does catch.
- **Does nothing for semantic duplication**, which is the collision that actually costs money.

So the timestamp solves the easy third of the problem at a readability cost. There are better trades.

## Recommended approach: sequential IDs, loud collisions

Keep `REQ-NNNN`. Add three things.

### 1. A shared counter file that every allocation must touch

Create `specification/requirements/next-req-id` containing a single line, for example `REQ-0300`. Every amendment that adds
requirements must bump this file in the same commit. The features directory already has this property by
accident: `feature-index.md` is a single shared file, so two branches adding features conflict at merge, and
`decision-index.md` does the same for decision records.

Now the scenario from the top produces a textual merge conflict on `next-req-id`. The second branch to merge
cannot land without a human looking at it. That is the Django leaf-node check, implemented with git.

### 2. A validator in CI

A small script, run on every merge request and on `main`, that parses all documents in `specification/requirements/` and
fails on:

- the same `REQ-NNNN` defined in two different documents with `revision: 1` (a true collision; the same ID
  legitimately appears in several documents only as increasing revisions);
- a later block whose `revision` is not greater than the earlier one;
- gaps in the sequence not listed in a known-skips file;
- `next-req-id` not equal to the highest ID present plus one;
- dangling `parents`, `retired-by`, `features`, or `decisions` references;
- a `ratified` requirement citing no feature or decision, or an `extracted` one citing either;
- a requirement citing a decision record whose status is not `accepted`;
- any change to a document that is not the newest one in the directory, other than moves to `archive/`;
- a `FEAT-XXXX` or `ADR-NNNN` in its index without a file, or a file without a row.

The counter file catches most collisions at merge time; the validator catches the ones that slip through a
careless conflict resolution, and everything else besides.

### 3. A renumber script for the losing branch

When the conflict fires, one branch has to move. Because IDs are referenced textually everywhere with a strict
pattern (`REQ-\d{4}`), renumbering is mechanical:

```
renumber-requirements --from REQ-0300 --to REQ-0304 --count 5 --paths specification/requirements/ specification/features/ specification/epics/
```

Rewrites the IDs in the amendment file, in `parents` fields that point at them, in feature files' Related
Requirements, and in epic `parent-requirements`. The script should refuse to run if any target ID already
exists. With that script, resolving a collision takes a minute and is not error-prone.

### Working rules

- **Allocate late.** Draft requirements with placeholder IDs (`REQ-????`) during the conversation. Assign real
  numbers in the last step before the Step 1 gate, right after a fresh `git fetch` and a look at `main`'s
  counter file. Shorter windows mean fewer collisions.
- **Merge requirement amendments promptly.** The amendment and feature file can merge before the epic is
  implemented. Requirements are decisions; they do not need to wait for code. This shrinks the window further
  and lets other branches build on the new IDs.
- **Consolidate on `main` only**, as its own merge request (see
  [docs/process/requirements-consolidation.md](requirements-consolidation.md)).

This is the right default for a small team, or a team with a few AI agents, running short-lived branches.

## Alternative: document-scoped IDs, unique without coordination

If branches are long-lived, or many agents author requirements in parallel with no human in the loop, the
coordination cost of renumbering becomes real. The Liquibase-style answer is to make the document part of the
identity:

```
REQ-202603101500-03
    └─ amendment document timestamp ─┘ └─ ordinal within that document
```

- Unique by construction: two authors collide only by creating amendments in the same minute **and** that
  collision is a filename conflict git already catches.
- Self-locating: the ID names the file that introduced the requirement, which is also what the `source` row
  in a consolidation records.
- Still readable in context: people say "the oh-three in the license-key amendment".
- No counter file, no renumbering, no coordination.

Costs: longer IDs in every table, loss of the global sequence as a rough timeline (the timestamp gives a finer
one), and a migration if an existing `REQ-NNNN` corpus must be converted. A mixed corpus (old `REQ-NNNN`, new
document-scoped) is workable since both match distinct patterns, but it is uglier than either alone. If you
are starting a fresh repository with many parallel authors, this is the scheme to pick from day one.

A middle option, if you want short IDs *and* no coordination: **reserve ID blocks per branch** by committing a
one-line bump of the counter file to `main` before authoring (`REQ-0300..0319 reserved for license-key-delivery`).
Unused IDs in a block become known skips. This works but adds a round-trip to `main` for every piece of work,
which in practice people forget.

## Semantic duplication

No identifier scheme prevents two branches from stating the same requirement under different IDs, or from
stating contradictory ones. Two existing steps in the process are where this is caught, and both should be
treated as the real defence:

1. **The Step 1 gate's conflict check.** The facilitator reviews each new requirement against the loaded corpus
   for dependencies, supersessions, and conflicts. Make the pre-flight read include **open merge requests'
   amendment files**, not only `main`, when more than one branch is active.
2. **The consolidation report.** Every consolidation lists near-duplicates and contradictions it noticed as
   candidate cleanups for a follow-up amendment. Running consolidation regularly turns duplicate detection into
   a routine rather than an accident.

## Decision summary

| Situation | Recommendation |
|---|---|
| Small team, short-lived branches, a few agents | Keep `REQ-NNNN`. Add the counter file, the CI validator, and the renumber script. Allocate late, merge amendments early. |
| Many long-lived branches or many unattended agents | Switch to document-scoped IDs `REQ-<doc-timestamp>-<ordinal>`. Keep the validator. |
| Either | Treat semantic duplication as the real risk. Read open MRs in pre-flight; consolidate on a schedule. |
| Millisecond timestamp IDs | Not recommended. Solves the least costly part of the problem at a permanent readability cost. |
