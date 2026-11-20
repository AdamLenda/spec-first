# Specification

Everything that says what this repository's software is supposed to be: why it exists, why it is built the
way it is, what it must do, and how it is to be built. The code that satisfies it is in [`../src/`](../src/README.md).

New to the repository? Start with [`../docs/getting-started.md`](../docs/getting-started.md).

| Directory | Answers | Contents | Changes by |
|---|---|---|---|
| [`features/`](features/README.md) | **Why**, business | One file per business capability (`FEAT-XXXX`), the feature template, and the feature index | Editing in place |
| [`decisions/`](decisions/README.md) | **Why**, technical | One architecture decision record per decision (`ADR-NNNN`), the template, and the decision index | Superseding; an accepted record is never rewritten |
| [`requirements/`](requirements/README.md) | **What** | The normative, numbered corpus (`REQ-NNNN`): a baseline plus dated amendments, and the document templates | Appending a new amendment; nothing is ever edited |
| [`epics/`](epics/README.md) | **How** | One folder per epic, each holding small, ordered stories, and the epic and story templates | Editing in place as work proceeds |

## Where a requirement comes from

| Source | Status | The requirement cites |
|---|---|---|
| A business capability | `ratified` | A feature file |
| A technical decision | `ratified` | An accepted decision record |
| The existing software | `extracted` | Neither |

## How a change moves through here

1. A conversation establishes the reason for the change → a file in `features/`, a record in `decisions/`, or
   both *(human approval)*
2. The conversation is refined into normative statements that cite it → a new amendment in `requirements/`
   *(human approval)*
3. The approved requirements are planned → an epic folder in `epics/` *(human approval)*
4. Each story is implemented in `src/`, and records the files it changed

The full procedure is in [`../docs/process/end-to-end-workflow.md`](../docs/process/end-to-end-workflow.md),
and a walkthrough of a first change is in [`../docs/getting-started.md`](../docs/getting-started.md). An agent
runs it with the [`refine-change`](../.claude/skills/refine-change/SKILL.md) skill.

## Current state

| Directory | State |
|---|---|
| `features/` | No features. Next ID `FEAT-0001` |
| `decisions/` | No decisions. Next ID `ADR-0001` |
| `requirements/` | No baseline. The first refinement writes it, starting at `REQ-0001` |
| `epics/` | No epics |

For what filled-in artifacts look like, see the fictional worked example in
[`../docs/examples/tideline/`](../docs/examples/tideline/README.md).
