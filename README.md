# spec-first

A working example of **specification-driven development**: the reason for a change, the requirements it
creates, and the plan to build it are written and approved as Markdown in the repository *before* the code,
and stay beside the code afterwards. Every artifact is designed to be read and written by both people and AI
coding agents.

## Start here

| You want to | Go to |
|---|---|
| **Understand the method in ten minutes** | [docs/getting-started.md](docs/getting-started.md): one change, walked through from idea to code |
| **See what the artifacts look like** | [docs/examples/tideline/](docs/examples/tideline/README.md): a complete worked example for a fictional store |
| **Try it** | Open this repository in [Claude Code](https://claude.com/claude-code) and describe a change. See [Try it](#try-it) below |
| **Use it in your own repository** | [docs/process/adopting-in-a-new-repo.md](docs/process/adopting-in-a-new-repo.md) |
| **Find a specific guide** | [All documentation](#all-documentation) below, or the index in [docs/README.md](docs/README.md) |

## The method

Every change answers three questions in writing, in order, and a person approves each answer before the next
is started.

| # | Question | Artifact | Lives in | Human gate |
|---|---|---|---|---|
| 1 | **Why** is this wanted? | A *feature file* for business value (`FEAT-XXXX`), or an *architecture decision record* for a technical choice (`ADR-NNNN`) | [`specification/features/`](specification/features/README.md), [`specification/decisions/`](specification/decisions/README.md) | Feature confirmed, or decision accepted |
| 2 | **What** must the system do? | Numbered, testable *requirements* (`REQ-NNNN`) that cite the feature or decision | [`specification/requirements/`](specification/requirements/README.md) | Requirement set approved |
| 3 | **How** will it be built? | An *epic* broken into small, independently mergeable *stories* | [`specification/epics/`](specification/epics/README.md) | Epic moved from `draft` to `ready` |
| 4 | Build it | Code, one story per commit; each story records the files it changed | [`src/`](src/README.md) | Ordinary code review |

```
   business discussion                      technical discussion
          │                                        │
          ▼                                        ▼
   ┌─────────────┐                          ┌─────────────┐
   │  features/  │  business why            │ decisions/  │  technical why
   └──────┬──────┘                          └──────┬──────┘
          │ ratifies                               │ ratifies
          └──────────────────┐   ┌─────────────────┘
                             ▼   ▼
                        ┌─────────────┐
     existing code ────▶│requirements/│  what the system SHALL do
       (extracted)      └──────┬──────┘
                               │ epics implement one or more requirements
                               ▼
                        ┌─────────────┐
                        │   epics/    │  small, sequentially-mergeable, feature-flagged stories
                        └──────┬──────┘
                               │ each story records the files it changed
                               ▼
                        ┌─────────────┐
                        │    src/     │
                        └─────────────┘
```

The result is a chain that can be followed in either direction: from a line of code to the story that wrote
it, the requirement it satisfies, and the reason that requirement exists.

### Three sources of a requirement

Every requirement comes from one of three places, and its status says which:

| Source | Status | The requirement cites |
|---|---|---|
| A business capability | `ratified` | A **feature file** describing the value proposition it supports |
| A technical decision | `ratified` | An accepted **decision record**: a design pattern adopted, a technology chosen, a policy on telemetry and observability |
| The existing software | `extracted` | Neither. It was inferred from code, with no feature or decision behind it |

A requirement may cite both a feature and a decision. One that cites neither is `extracted`, and stays that
way until someone writes down the reason for it.

### Core ideas

1. **Requirements are the center of gravity.** Features and decisions explain why they exist; epics explain
   how they get built. Every requirement has a stable, globally unique ID that never changes and is never
   reused.
2. **Provenance is checkable.** `ratified` means the requirement cites a feature or an accepted decision;
   `extracted` means it cites neither. The label is a fact about the document, not a judgement.
3. **The requirements corpus is append-only.** A baseline is written once. All later changes are dated
   amendment documents. The current state of any requirement is the baseline plus amendments applied in
   filename order, like reading a git log. Periodic
   [consolidation](docs/process/requirements-consolidation.md) keeps the corpus readable without breaking
   that model.
4. **Accepted decisions are immutable.** A decision is changed by a new record that supersedes the old one, so
   the reasoning a requirement was ratified against is never rewritten.
5. **Epics and stories are point-in-time.** They are edited in place and record what was understood when the
   work was planned and done, including the files actually modified.
6. **Stories are sized for an AI agent's context window.** Zero to three points, independently mergeable,
   deployable behind a feature flag.
7. **Every stage has a human gate.** Features, decisions, requirement sets, and epics each require explicit
   human approval before the next stage begins.

## Try it

The process is run by three agent skills in [`.claude/skills/`](.claude/skills/README.md). Claude Code loads
them when you open the repository.

| Skill | Run it | What it does |
|---|---|---|
| [`refine-change`](.claude/skills/refine-change/SKILL.md) | Describe a change in plain language, or type `/refine-change` | Takes the change through all four steps above, stopping at each gate for your approval |
| [`extract-requirements`](.claude/skills/extract-requirements/SKILL.md) | `/extract-requirements` | Once, on a codebase that already exists: writes the baseline requirements from the code |
| [`consolidate-requirements`](.claude/skills/consolidate-requirements/SKILL.md) | `/consolidate-requirements` | Folds accumulated requirements documents into one, without changing any meaning |

This repository is at its starting point: the process is in place, the specification is empty, and `src/`
holds no code. Because there is no code yet, the first change you refine produces the baseline requirements
document, starting at `REQ-0001`.

Using a different agent? Each `SKILL.md` is plain Markdown. Give the agent the file as its instructions,
together with [`CLAUDE.md`](CLAUDE.md).

## All documentation

**Learn the method**

| Document | Read it when |
|---|---|
| [docs/getting-started.md](docs/getting-started.md) | You are new and want one change walked through |
| [docs/examples/tideline/](docs/examples/tideline/README.md) | You want to see every artifact filled in |
| [docs/process/end-to-end-workflow.md](docs/process/end-to-end-workflow.md) | You want the full loop and the reasoning behind each step |

**Write one kind of artifact**

| Document | Read it when |
|---|---|
| [specification/features/README.md](specification/features/README.md) | You need to describe the business value of a change |
| [specification/decisions/README.md](specification/decisions/README.md) | You need to record a technical decision: a pattern, a technology, a cross-cutting policy |
| [specification/requirements/README.md](specification/requirements/README.md) | You need the requirement template, status vocabulary, tag taxonomy, or amendment rules |
| [specification/epics/README.md](specification/epics/README.md) | You are planning or executing implementation |

**Handle a specific situation**

| Document | Read it when |
|---|---|
| [docs/process/adopting-in-a-new-repo.md](docs/process/adopting-in-a-new-repo.md) | You want to set this up in another repository |
| [docs/process/requirements-consolidation.md](docs/process/requirements-consolidation.md) | The corpus has grown and you want to compact it without losing history |
| [docs/process/concurrent-branches-and-id-collisions.md](docs/process/concurrent-branches-and-id-collisions.md) | Several branches or agents author requirements in parallel |

**Work with an AI agent**

| Document | Read it when |
|---|---|
| [.claude/skills/README.md](.claude/skills/README.md) | You want to know what each skill does and how to run it |
| [docs/agent-instructions/README.md](docs/agent-instructions/README.md) | You are an AI agent, or are directing one, and want the rules for one task |
| [CLAUDE.md](CLAUDE.md) | You want the rules an agent follows in this repository |

## Repository layout

The root groups files by purpose. Every directory has a README that explains what belongs in it.

```
specification/          WHAT is being built, and why and how
  features/             ← WHY, business:  value statements                (FEAT-XXXX)
  decisions/            ← WHY, technical: architecture decision records   (ADR-NNNN)
  requirements/         ← WHAT: normative, numbered, append-only corpus   (REQ-NNNN)
  epics/                ← HOW:  implementation plans broken into stories  (YYYY.MM.DD-epic-name/NN-story.md)
src/                    The software the specification describes
docs/                   How to work in this repository
  getting-started.md    ← Walkthrough of a first change
  process/              ← Guides that span the whole workflow
  agent-instructions/   ← Task-by-task rules for AI agents
  examples/tideline/    ← A worked example for a fictional store
.claude/skills/         The procedures an agent runs: refine-change, extract-requirements, consolidate-requirements
CLAUDE.md               Entry point for AI agents
```

## About the worked example

[`docs/examples/tideline/`](docs/examples/tideline/README.md) follows one change through a feature file, a
decision record, an amendment, an epic, and a completed story. **Tideline** is a fictional e-commerce
platform; it, its domains, file paths, and requirement text are invented to illustrate the process and are
not part of this repository's specification.
