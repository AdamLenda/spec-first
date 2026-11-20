# Getting started

A walkthrough of one change, from an idea to merged code, for someone who has not used this process before.
It takes about ten minutes to read. For the rationale behind each step, see
[process/end-to-end-workflow.md](process/end-to-end-workflow.md).

## The idea in one paragraph

Before code is written, three things are written down and approved by a person: **why** the change is wanted,
**what** the system must do as a result, and **how** it will be built. Each is a Markdown file in
[`../specification/`](../specification/README.md), and each links to the one before it. An AI agent does most
of the writing; a person approves each stage. Afterwards, any line of code can be traced back to the
requirement it satisfies and the reason that requirement exists.

## What you need

- This repository, cloned.
- An AI coding agent with access to it. The repository is set up for
  [Claude Code](https://claude.com/claude-code), which loads the procedures in
  [`../.claude/skills/`](../.claude/skills/README.md) by itself. With another agent, give it
  [`../.claude/skills/refine-change/SKILL.md`](../.claude/skills/refine-change/SKILL.md) as its instructions.

## The walkthrough

### 1. Describe the change

Open the repository in the agent and say what you want, in ordinary language:

> Customers who buy software should receive a license key by email.

In Claude Code, the `refine-change` skill starts by itself. You can also start it with `/refine-change`. The
agent reads the existing specification first, and it writes no code yet.

### 2. Say why: a feature or a decision

The agent asks what the change is in service of. There are two possible answers, and a change can have both:

| The reason is | The agent writes | In |
|---|---|---|
| A business capability: something a user or the business gains | A **feature file** | [`specification/features/`](../specification/features/README.md) |
| A technical choice: a pattern, a technology, a policy such as observability | A **decision record** (ADR) | [`specification/decisions/`](../specification/decisions/README.md) |

It asks questions until the value, scope, and success criteria are clear, then shows you the draft.

**Gate 1: you confirm the feature file, or accept the decision record.**

### 3. Say what: requirements

The agent turns the discussion into numbered, testable statements, such as "The system SHALL email the
license key within 60 seconds of payment confirmation". It asks about security, performance, observability,
and backward compatibility even if you did not raise them. Each requirement cites the feature or decision
from step 2; that citation is what makes it `ratified`.

The requirements go in a new, dated file in
[`specification/requirements/`](../specification/requirements/README.md). Existing requirements files are
never edited. If the change alters or removes an earlier requirement, the new file says so.

**Gate 2: you approve the requirement set.**

### 4. Say how: an epic and its stories

The agent plans the work as an **epic**: a folder in [`specification/epics/`](../specification/epics/README.md)
holding small, ordered **stories**. Each story is small enough to implement in one sitting, can be merged
without the others, and sits behind a feature flag. Each has acceptance criteria and notes detailed enough
for an agent that has not seen this conversation.

**Gate 3: you review the plan and move the epic from `draft` to `ready`.**

### 5. Build

The agent implements one story at a time, in [`../src/`](../src/README.md), one commit per story. Each
commit includes the story file, updated with the list of files that changed. That list is the link from the
requirement to the code.

### 6. Close

When the last story is done, the epic is marked `completed` and the feature `active`. If building it showed
that a requirement or a decision was wrong, that is corrected by a new requirements file or a new decision
record, not by editing the old one.

## What you end up with

```
specification/
  features/digital-delivery/license-key-delivery.md         why (business)
  decisions/0003-idempotent-webhook-handling.md             why (technical)
  requirements/202603101500-license-key-delivery.md         what
  epics/2026.03.10-license-key-delivery/                    how
    README.md
    01-add-entitlement-table-and-repository.md
    ...
src/                                                         the code, listed in each story
```

The names above come from the worked example. That change is carried through in full, for a fictional store,
in [examples/tideline/](examples/tideline/README.md), where you can read each of these files.

## The other two procedures

| Situation | Procedure |
|---|---|
| You are adopting this on a codebase that already exists, and it has no requirements yet | `/extract-requirements` reads the code and writes a baseline. Requirements with no feature or decision behind them are marked `extracted`. See [process/end-to-end-workflow.md](process/end-to-end-workflow.md#part-1-bootstrap-a-baseline) |
| The requirements files have piled up and reading them all is slow | `/consolidate-requirements` folds them into one document without changing any meaning. See [process/requirements-consolidation.md](process/requirements-consolidation.md) |

## Where to go next

| You want to | Read |
|---|---|
| See filled-in examples of every artifact | [examples/tideline/](examples/tideline/README.md) |
| Understand why the process is shaped this way | [process/end-to-end-workflow.md](process/end-to-end-workflow.md) |
| Use this in your own repository | [process/adopting-in-a-new-repo.md](process/adopting-in-a-new-repo.md) |
| Look up the format of one artifact | The README in its directory under [`../specification/`](../specification/README.md) |
| Find everything else | [README.md](README.md), the documentation index |
