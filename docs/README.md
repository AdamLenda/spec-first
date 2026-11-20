# Documentation

Everything written about how to work in this repository, in one index. What the software must do is in
[`../specification/`](../specification/README.md); this directory explains the method.

**New here? Read [getting-started.md](getting-started.md).** It walks through one change from idea to code.

## Learn the method

| Document | What it gives you |
|---|---|
| [getting-started.md](getting-started.md) | A ten-minute walkthrough of one change, and what you type to start one |
| [examples/tideline/](examples/tideline/README.md) | Every artifact filled in for one change to a fictional store: feature file, decision record, requirements amendment, epic, completed story |
| [process/end-to-end-workflow.md](process/end-to-end-workflow.md) | The full loop with the reasoning for each step: bootstrap a baseline, then refine → gate → plan → build |

## Look up one kind of artifact

The guide for each artifact is the README of the directory that holds it, beside its template.

| Artifact | Answers | Guide |
|---|---|---|
| Feature file (`FEAT-XXXX`) | Why, for the business | [specification/features/README.md](../specification/features/README.md) |
| Decision record (`ADR-NNNN`) | Why, technically | [specification/decisions/README.md](../specification/decisions/README.md) |
| Requirement (`REQ-NNNN`) | What the system shall do | [specification/requirements/README.md](../specification/requirements/README.md) |
| Epic and story | How it gets built | [specification/epics/README.md](../specification/epics/README.md) |

## Handle a specific situation

| Situation | Guide |
|---|---|
| Setting the process up in another repository | [process/adopting-in-a-new-repo.md](process/adopting-in-a-new-repo.md) |
| The requirements corpus has grown and is slow to read | [process/requirements-consolidation.md](process/requirements-consolidation.md) |
| Several branches or agents author requirements at once | [process/concurrent-branches-and-id-collisions.md](process/concurrent-branches-and-id-collisions.md) |

## Run it with an AI agent

| Document | What it is |
|---|---|
| [`.claude/skills/`](../.claude/skills/README.md) | The three executable procedures: `refine-change`, `extract-requirements`, `consolidate-requirements` |
| [agent-instructions/](agent-instructions/README.md) | Short, imperative rules for each task, condensed from the guides. The guides are authoritative; these are derived from them |
| [`CLAUDE.md`](../CLAUDE.md) | The agent's entry point: the rules for this repository and its project context |

## Documentation for the software

Nothing yet. User and operator documentation for the solution in `src/` belongs in this directory, beside
`process/`, once there is something to document.
