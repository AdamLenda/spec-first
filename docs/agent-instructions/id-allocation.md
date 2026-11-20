# ID Allocation and Concurrent Branches

How to take a `REQ-NNNN` without colliding with another branch. Digest of
`docs/process/concurrent-branches-and-id-collisions.md`.

## The failure to avoid

Two branches read the same next ID and each defines it in a different file. Git merges both cleanly, and the
corpus now holds two unrelated requirements under one ID.

## Allocating

1. **Draft with placeholders.** Use `REQ-????` during the conversation.
2. **Allocate late.** Assign real numbers as the last step before the requirements gate, right after
   `git fetch` and a look at the main branch.
3. **Find the next ID:**
   - if `specification/requirements/next-req-id` exists, it is the authority. Take IDs from it and bump it **in the same
     commit** as the amendment;
   - otherwise use the `next-req-id` in the newest requirements document on the main branch.
4. **Record it.** Set `next-req-id` in the new amendment's metadata to the first unused ID.
5. **Merge the amendment promptly.** It can land before the epic is implemented.

Feature and decision IDs need no counter file: `feature-index.md` and `decision-index.md` are each a single
shared file that conflicts when two branches add a row.

## When a collision happens

A merge conflict on the counter file, or a validator failure for the same ID at `revision: 1` in two
documents, means the later branch renumbers. Never resolve it by keeping both.

1. Pick the new starting ID from the main branch's counter.
2. Rewrite the losing branch's IDs everywhere they appear: the amendment's headings, `parents` and
   `retired-by` fields that point at them, feature files' Related Requirements, epic `parent-requirements`,
   and story text.
3. Refuse to proceed if any target ID already exists.
4. Bump the counter and re-run the validator.

Use the repository's renumber script if it has one; the pattern is strictly `REQ-\d{4}`.

Renumbering is allowed only before merge. A merged ID is permanent.

## Duplicate meaning is the larger risk

No ID scheme stops two branches from stating the same requirement under different IDs, or contradictory
ones. The defences are procedural:

- at the requirements gate, check each new requirement against the corpus **and** against amendment files in
  open merge requests;
- when consolidating, report near-duplicates and contradictions as candidate cleanups.

## Which scheme a repository uses

Check the decisions log in `specification/requirements/README.md` before assuming.

| Situation | Scheme |
|---|---|
| Small team, short-lived branches, a few agents (default) | `REQ-NNNN` with counter file, CI validator, renumber script |
| Many long-lived branches or unattended agents | Document-scoped `REQ-<doc-timestamp>-<ordinal>`, e.g. `REQ-202603101500-03`; no counter, no renumbering |
| Millisecond-timestamp IDs | Not used |

## What the validator enforces

If the repository has one, expect it to fail on:

- the same ID at `revision: 1` in two documents;
- a later block whose `revision` is not greater than the earlier one;
- sequence gaps that are not recorded as known skips;
- `next-req-id` not equal to the highest ID plus one;
- dangling `parents`, `retired-by`, `features`, or `decisions` references;
- a `ratified` requirement citing no feature or decision, or an `extracted` one citing either;
- a requirement citing a decision that is not `accepted`;
- any change to a requirements document other than the newest, apart from moves to `archive/`;
- a feature or decision index row without a file, or a file without a row.

Run it before presenting an amendment.
