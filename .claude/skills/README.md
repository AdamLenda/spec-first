# Skills

The three procedures of the specification-first process, written for an AI agent to execute.
[Claude Code](https://claude.com/claude-code) loads them from this directory. Each `SKILL.md` is plain
Markdown, so any other agent can be given the file as its instructions.

| Skill | Run it | What it does | Rationale |
|---|---|---|---|
| [`refine-change`](refine-change/SKILL.md) | Describe a change, or `/refine-change` | Takes a change from an idea to merged code: feature or decision record → requirements → epic and stories → implementation, with a human approval at each stage | [End-to-end workflow, Part 2](../../docs/process/end-to-end-workflow.md#part-2-the-refinement-loop) |
| [`extract-requirements`](extract-requirements/SKILL.md) | `/extract-requirements` | Once per repository: reads an existing codebase and writes the baseline requirements document | [End-to-end workflow, Part 1](../../docs/process/end-to-end-workflow.md#part-1-bootstrap-a-baseline) |
| [`consolidate-requirements`](consolidate-requirements/SKILL.md) | `/consolidate-requirements [cut-off document ID]` | Folds the baseline and its amendments into one document without changing any requirement's meaning | [Requirements consolidation](../../docs/process/requirements-consolidation.md) |

The skills contain nothing specific to one product. They read the stack and conventions from the Project
context section of [`../../CLAUDE.md`](../../CLAUDE.md), so they can be copied to another repository
unchanged.

New to the process? Start with [`../../docs/getting-started.md`](../../docs/getting-started.md).
