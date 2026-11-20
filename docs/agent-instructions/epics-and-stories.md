# Epics and Stories

An epic states **how**: an implementation plan made of small stories. Digest of `specification/epics/README.md`.
Templates: `specification/epics/epic-readme-template.md`, `specification/epics/story-template.md`. Example:
`docs/examples/tideline/specification/epics/2026.03.10-license-key-delivery/`.

Epics and stories are point-in-time documents. Edit them in place so they say what was understood and what
was done.

## Before planning

The requirements must be approved. An epic drafted before that plans the wrong thing.

## Define the feature flag first

Every epic that introduces new behaviour ships behind a flag. Before writing stories, decide and record in
the epic README:

- **name**, following the project's existing convention;
- **default state**: off in production at launch;
- **removal criteria**: the observable condition under which the flag is deleted.

If there is no flag, the Constraints section says why. Flag removal is itself a story, usually the last.

## Create the epic

1. Create `specification/epics/YYYY.MM.DD-kebab-name/`, dated when the epic was created or refined.
2. Copy the epic template to `README.md`.
3. Fill the metadata:

   | Field | Value |
   |---|---|
   | `name` | kebab-case |
   | `refined-by` | Who refined it |
   | `lifecycle` | `draft` |
   | `parent-requirements` | REQ IDs separated by ` · `. At least one |
   | `features` | FEAT IDs separated by ` · `, or `—` |
   | `decisions` | ADR IDs the epic implements or must conform to, separated by ` · `, or `—` |
   | `feature-flags` | Flag names separated by ` · `, or `—` with the reason in Constraints |
   | `references` | Links to the feature, decision records, the amendment, designs |

4. Write **Goal** (one or two sentences), **Scope** (Included / Not included), **Constraints** (limits,
   dependencies, assumptions, story sequencing, flag default and removal criteria), **Stories** (links).

## Write stories

File: `NN[letter]-kebab-name.md`, zero-padded. Insert a missed step as `01a`, `01b` rather than renumbering.

Each story must be:

- **0–3 points.** More than a few hundred changed lines, or more than one layer touched for more than one
  reason, means two stories.
- **Independently mergeable.** The main branch stays deployable after each one.
- **Behind the flag.** New behaviour ships dark.
- **Self-contained.** Implementation Notes plus the referenced files are enough to do the work.

Order: pure additions and structural refactors first, behaviour switched on later. A typical sequence:

1. Schema or migration, additive only
2. Repository or data access
3. Domain service with tests
4. API surface, flagged off
5. Wire-up and flag enablement criteria

Write the sections in this order:

1. **Acceptance Criteria.** A checklist specific enough that the implementer knows when to stop and a
   reviewer can verify it. Cite the REQ ID a criterion satisfies. Include "all existing tests pass" and "lint
   passes".
2. **Implementation Notes.** Written for a reader with no memory of the planning conversation and a limited
   context window: file paths, signatures, order of edits, existing code to copy the pattern from.
3. **Files Modified.** Leave empty.

Metadata: `name`, `description` (one sentence), `lifecycle: draft`, `story-points`, `references` (the files to
open first).

## Gate

Present the epic README and the story list with point estimates. A human moves the epic and stories from
`draft` to `ready`. An agent does not set `ready` on its own work.

## Execute a story

Only when it is `ready`.

1. Set `lifecycle: in-progress`.
2. Read the story, its references, and the decision records its epic lists; the implementation must conform
   to them. You should not need the planning conversation; if you do, the story is
   underspecified. Stop and say so.
3. Implement. Run lint and tests.
4. Tick each acceptance criterion as it is met.
5. Fill Files Modified from `git diff --name-only`, marking each `(new)` or `(modified)`.
6. Set `lifecycle: completed`.
7. Commit the story file together with the code: one story, one commit.

Stay inside the story. Work it did not describe goes in a new story.

## Lifecycle

| State | Meaning | Who moves it there |
|---|---|---|
| `draft` | Being planned | Author |
| `ready` | Reviewed and approved | A human reviewer |
| `in-progress` | Implementation begun | Implementer |
| `completed` | Criteria met, tests pass, merged, Files Modified filled | Implementer |
| `abandoned` | Stopped by decision; kept for audit with a one-line reason | Whoever decides |

When the last story completes, set the epic to `completed` and the feature to `active`.
