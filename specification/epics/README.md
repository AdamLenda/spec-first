# Epics and Stories

The `specification/epics/` directory holds **implementation planning artifacts**. An epic is a focused body of work that
implements one or more requirements; it is composed of small, sequentially mergeable stories that can be
continuously integrated and deployed while hidden behind feature flags.

Unlike requirements, epics and stories are **point-in-time** documents. They are edited in place so that they
accurately describe what was understood when the work was planned and executed. They do not use the amendment
model.

## Directory layout

```
specification/epics/
  README.md                        ← Explains this directory
  epic-readme-template.md          ← Template for an epic's README
  story-template.md                ← Template for a story
  YYYY.MM.DD-epic-name/            ← One folder per epic
    README.md                      ← Epic metadata and narrative
    01-story-name.md               ← Story 1
    01a-story-name.md              ← Story 1a, inserted between 01 and 02 without renaming
    02-story-name.md               ← Story 2
```

### Naming

- **Epic folders:** `YYYY.MM.DD-kebab-case-name`. The date is when the epic was created or refined. Example:
  `2026.03.10-license-key-delivery`.
- **Story files:** `<zero-padded-number><optional-letter>-kebab-case-name.md`. The optional letter lets you
  insert a story mid-sequence (`01a`, `01b`) when planning reveals a missing step.

## Epic README

### Metadata

| Field | Type | Description |
|---|---|---|
| `name` | kebab-case | Short machine-readable identifier |
| `refined-by` | handle or name | Who refined and created the epic |
| `lifecycle` | enum | `draft`, `ready`, `in-progress`, `completed`, `abandoned` |
| `parent-requirements` | REQ IDs | Requirements this epic implements, ` · ` separated. `—` if none |
| `features` | FEAT IDs | Features this epic contributes to, ` · ` separated. `—` if none |
| `decisions` | ADR IDs | Decision records this epic implements or must conform to, ` · ` separated. `—` if none |
| `feature-flags` | flag names | Flags that gate this epic's behaviour, ` · ` separated. Empty or `—` if none, with the reason stated in Constraints |
| `references` | markdown links | Feature files, decision records, designs, tickets, documentation |

### Narrative sections

- **Goal.** One or two sentences: what problem this epic solves.
- **Scope.** Explicit **Included** and **Not included** lists.
- **Constraints.** Known limitations, dependencies, assumptions, sequencing rules between stories.
- **Stories.** Links to the numbered story files.

## Story

### Metadata

| Field | Type | Description |
|---|---|---|
| `name` | kebab-case | Short machine-readable identifier |
| `description` | string | One-sentence summary of what the story delivers |
| `lifecycle` | enum | Same vocabulary as epics |
| `story-points` | integer | 0–3 recommended |
| `references` | markdown links | Code files, tests, and decision records the implementer should open first |

### Sections

- **Acceptance Criteria.** A checklist of conditions that must be met. Specific enough that an implementer
  can tell when they are done and a reviewer can verify it. Include "all existing tests pass" and "lint passes"
  when they apply.
- **Implementation Notes.** Context, patterns to follow, constraints, design decisions, and anything an
  implementer with no memory of the planning conversation needs. This section is written for an AI agent with
  a limited context window: be concrete about file locations, signatures, and the order of edits.
- **Files Modified.** Blank during drafting. After implementation, list every file created or changed, with
  `(new)` or `(modified)`. Generate it from `git diff --name-only`. This is the audit trail that connects
  requirements to code.

## Sizing stories

Stories are sized for two consumers: a CI/CD pipeline and an AI coding agent with a bounded context window.

- **0–3 points.** A story that needs more than a few hundred lines of change, or touches more than one layer
  for more than one reason, is two stories.
- **Independently mergeable.** Each story leaves the main branch deployable. Order stories so that
  earlier ones are pure additions or structural refactors and later ones switch behaviour on.
- **Behind a flag.** New behaviour ships dark. The flag is defined in the epic before stories are planned.
- **Self-contained.** The story's Implementation Notes plus the files it references should be enough to do
  the work without reading the planning conversation.

A typical sequence for a new capability:

1. Schema or migration (additive only).
2. Repository or data access.
3. Domain service with tests.
4. API surface, flagged off.
5. Wire-up and flag enablement criteria.

## Feature flags

Every epic that introduces new behaviour is gated behind a feature flag unless the epic's Constraints section
explains why not (for example, behaviour that is already scoped by runtime environment). Before decomposing
stories, define:

- **Flag name**, following the project's existing naming convention.
- **Default state**, off in production at launch.
- **Removal criteria**, the observable condition under which the flag can be permanently deleted.

Record all three in the epic README. Flag removal is itself a story, usually the last one.

## Lifecycle states

| State | Meaning |
|---|---|
| `draft` | Being refined and planned. Not ready for implementation. |
| `ready` | A human has reviewed, verified, and approved it. Ready for implementation. |
| `in-progress` | Implementation has begun. |
| `completed` | All acceptance criteria met, tests pass, code merged. |
| `abandoned` | Work was stopped by decision. Retained for audit, with a brief note explaining why. |

Transitions:

- **draft → ready**: a human reviews for clarity, feasibility, and alignment with the parent requirements.
- **ready → in-progress**: a developer or agent begins work and updates the lifecycle field.
- **in-progress → completed**: acceptance criteria met, tests pass, merged, Files Modified populated.
- **any → abandoned**: add a one-line comment explaining the decision.

## Workflow

### Creating an epic

1. Create `specification/epics/YYYY.MM.DD-epic-name/`.
2. Copy [`specification/epics/epic-readme-template.md`](epic-readme-template.md) to `README.md` with `lifecycle: draft`.
3. Fill in Goal, Scope, Constraints, parent requirements, features, decisions, and feature flag.
4. Add stories as they are refined.

### Creating a story

1. Create `NN<letter>-story-name.md` from [`specification/epics/story-template.md`](story-template.md) with `lifecycle: draft`.
2. Write acceptance criteria first, then implementation notes and references.
3. Leave Files Modified empty.

### Executing a story

1. Set `lifecycle: in-progress`.
2. Implement, run lint and tests.
3. Tick the acceptance criteria boxes as they are met.
4. Populate Files Modified from `git diff --name-only`.
5. Set `lifecycle: completed` and commit the story file with the code it describes, so the planning artifact
   and the change are reviewed together.

## Best practices

- Keep stories small.
- Feature-flag every epic, or write down why not.
- Link every epic to at least one parent requirement.
- Update lifecycle fields as you go; they are the at-a-glance status.
- Record files modified after implementation.
- Use relative repository paths in references so readers can jump straight to code.

See the worked example: [`docs/examples/tideline/specification/epics/2026.03.10-license-key-delivery/`](../../docs/examples/tideline/specification/epics/2026.03.10-license-key-delivery/).
