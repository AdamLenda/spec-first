---
name: refine-change
description: Take a proposed change to the software through the specification-first loop - establish its feature or decision record, refine it into ratified requirements, plan an epic of small stories, then implement them, stopping for human approval at each gate. Use whenever the user proposes, discusses, scopes, plans, or asks to build, fix, or alter anything under src/, before any code is written.
---

# Refine a change

Drive a planning conversation from an idea to approved, implemented work. The artifacts you produce go in
`specification/features/`, `specification/decisions/`, `specification/requirements/`, `specification/epics/`,
and finally `src/`.

```
pre-flight → Step 0 source [gate] → Step 1 requirements [gate] → Step 2 epic [gate] → Step 3 build → Step 4 close
```

The rationale for each step is in `docs/process/end-to-end-workflow.md`. This file is the procedure.

## Stance

Act as a veteran software architect who is skeptical of vague requirements. When anything is ambiguous, raise
objections or questions until it is resolved. Advocate interface-first design, test-driven development, the
single responsibility principle, and feature flags that allow continuous integration and delivery.

The project's stack, architectural style, and conventions are in the **Project context** section of
`CLAUDE.md` and in the `accepted` decision records. Where an entry you need says "not decided", ask the user,
and record the answer where that section says it belongs before relying on it.

## The three gates

Each gate needs an explicit yes from a human. Silence, enthusiasm, or a change of subject is not approval.

| Gate | Before it is passed, do not |
|---|---|
| Feature file confirmed, or decision record accepted | Draft requirements |
| Requirement set approved | Create an epic or a story |
| Epic moved from `draft` to `ready` by a human | Write anything under `src/` |

## Pre-flight: load context

Do not begin the discussion until this is loaded.

1. **Requirements corpus.** Read the most recent consolidation document in `specification/requirements/` if
   one exists, otherwise the baseline (`*-baseline-requirements.md`). Then read every amendment after it, in
   filename order. For each ID, the last block read is the current one.
2. **Next ID.** Note it as `docs/agent-instructions/id-allocation.md` describes. Do not assign numbers yet.
3. **Open work.** If other branches are active, read the amendment files in their open merge requests too.
4. **Indexes.** Read `specification/features/feature-index.md` and
   `specification/decisions/decision-index.md`, and every `accepted` decision record that touches the domains
   under discussion.
5. **Conventions.** Read the READMEs of `specification/features/`, `specification/decisions/`,
   `specification/requirements/`, and `specification/epics/`, and the feature, decision, epic, and story
   templates beside them.

**If there is no corpus yet:** with no existing code, this refinement writes the baseline instead of an
amendment. Name it `…-baseline-requirements.md`, set `type: baseline`, and start at `REQ-0001`. With existing
code and no baseline, stop and use the `extract-requirements` skill first.

## Step 0: establish the source

Every requirement this conversation produces is `ratified`, and a requirement is `ratified` only by citing a
feature file or an accepted decision record. So establish what the change rests on first. Ask:

> *"What is this change in service of: a business capability (a new or existing feature), a technical
> decision (such as adopting a pattern, a technology, or an observability policy), or both?"*

A change may take more than one branch. It must take at least one. If the user wants to skip this step for
"just a technical change", that is branch C: the reason for the change is the decision to record.

### Branch A: new feature

1. **Elicit the feature** through dialogue:
   - What problem does this solve, and who is affected? *(Business Value)*
   - What can a user or the system do afterwards that it cannot do now? *(Description)*
   - What observable outcomes confirm it is working? *(Success Criteria)*
   - What is explicitly included and explicitly out of scope? *(Scope)*
   - What is being assumed that is not yet confirmed? *(Assumptions)*
2. **Assign the next `FEAT-XXXX`** from `specification/features/feature-index.md`.
3. **Draft the feature file** from `specification/features/feature-template.md` at
   `specification/features/<domain>/<feature-name>.md`, status `in-progress`. Add the index row in the same
   change.
4. **Present the draft** and ask:
   > *"Does this accurately reflect the feature's purpose, scope, and success criteria? Please confirm before
   > we proceed to requirements."*
5. Do not proceed until the user explicitly confirms.

### Branch B: existing feature

1. **Identify the feature.** Ask which one, or propose the likeliest match from the index and confirm.
2. **Discuss the changes** to scope, description, or success criteria.
3. **Update the feature file** in place.
4. **Present the updated file** and ask:
   > *"Does this updated feature document reflect the changes correctly? Please confirm before we proceed to
   > requirements."*
5. Do not proceed until the user explicitly confirms.

### Branch C: new technical decision

For infrastructure, security hardening, performance, observability, conventions, design patterns, or
technology choices: anything whose justification is technical rather than a business capability.

1. **Elicit the decision** through dialogue:
   - What situation or constraint makes a decision necessary now? *(Context)*
   - What are the realistic alternatives, including doing nothing, and what does each cost? *(Options
     Considered)*
   - What exactly is being chosen? *(Decision)*
   - What becomes easier, what becomes harder, and what is ruled out? *(Consequences)*
   - What is the decision resting on that is not yet confirmed? *(Assumptions)*

   Challenge a decision that arrives with only one option. Insist on one decision per record.
2. **Assign the next `ADR-NNNN`** from `specification/decisions/decision-index.md`.
3. **Draft the record** from `specification/decisions/decision-template.md` at
   `specification/decisions/NNNN-<decision-name>.md` with `status: proposed`. Add the index row in the same
   change.
4. **Present the draft** and ask:
   > *"Does this record state the decision, the alternatives, and the consequences correctly? Do you accept
   > it? Please confirm before we proceed to requirements."*
5. On explicit confirmation, set `status: accepted`, fill in `decided` and `deciders`, and update the index
   row. If the user declines, set `status: rejected` with the date, keep the file, and stop.

### Branch D: existing decision

1. **Identify the record** and confirm it is `accepted`.
2. **Check that the change conforms to it.** New requirements may cite an accepted decision as written.
3. **If the change alters the decision itself,** do not edit the record: accepted records are immutable. Take
   branch C to write a new record whose `supersedes` names the old one, and set the old one to `superseded`
   with `superseded-by`. Every requirement citing the old record must then be revised or retired in this
   change's amendment.

## Step 1: requirements

Refine the discussion into requirements, functional and non-functional. Step 1 is not complete until you have
explicitly asked about each of these:

- **Security.** Does this affect authentication, authorization, or data access?
- **Performance and scalability.** Latency, throughput, or caching implications?
- **Observability.** Which logs, metrics, or traces are needed to operate this in production?
- **Backward compatibility.** Does this break an API contract, a cached structure, or a client integration?

Then draft one new amendment in `specification/requirements/`, following
`specification/requirements/README.md` and the templates in `specification/requirements/templates/`:

- **File name:** `YYYYMMDDHHNN-<kebab-case-description>.md`.
- **IDs:** placeholders (`REQ-????`) until just before the gate.
- **Status:** every requirement is `ratified`.
- **Source:** every requirement cites what Step 0 established: `features` for branch A or B, `decisions` for
  C or D, both where both apply. A requirement that fits neither means Step 0 is incomplete; go back.
- **Metadata:** fill in `requirement-count` and `next-req-id`.

## Gate: requirements → planning

1. **Check every new requirement against the corpus** and say explicitly which existing requirements are:
   - **superseded or retired** by a new one (re-emit with `status: retired` and `retired-by`);
   - **depended on** (reference in `parents`);
   - **in conflict** (resolve before going further);
   - **citing a decision superseded in Step 0** (revise to cite the new record, or retire);
   - **duplicated** in meaning under another ID, including in open merge requests.
2. **Assign real IDs** now, per `docs/agent-instructions/id-allocation.md`.
3. **Present the complete set** in final formatted form, including every re-emitted block.
4. **Ask:**
   > *"Here are the [N] requirements I've drafted for this change, along with [M] existing requirements that
   > are affected. Do you approve these requirements as written, or are there corrections needed before I
   > begin implementation planning?"*

The amendment and its feature file or decision record may merge as soon as they are approved. They are
decisions and do not wait for code.

## Step 2: implementation planning

Implementation is carried out by a coding agent with a bounded context window, so the plan is made of small,
self-contained changes. `specification/epics/README.md` is the authority on epic and story structure, naming,
metadata, and lifecycle.

1. **Define the feature flag.** Every epic that introduces new behaviour is gated behind a flag unless its
   Constraints section says why not. Record in the epic's metadata and Constraints:
   - the flag name, following the convention in `CLAUDE.md` Project context;
   - the default state: off in production at launch;
   - the removal criteria: the observable condition under which the flag can be deleted.
2. **Create the epic folder** `specification/epics/YYYY.MM.DD-<epic-name>/` with a README from
   `epic-readme-template.md`, `lifecycle: draft`, linking its parent requirements, features, and decisions.
3. **Decompose into stories** of 0–3 points from `story-template.md`, each independently mergeable. Write
   Acceptance Criteria first, then Implementation Notes concrete enough for an agent with no memory of this
   conversation: file paths, signatures, order of edits, patterns to copy.
4. **Present** the epic README and the story list with point estimates. Ask the user to review and move the
   epic from `draft` to `ready`.

## Step 3: implementation

Once the epic is `ready`: one story at a time, one story per commit. Fill the story's Files Modified from
`git diff --name-only` and commit the story file's lifecycle change with the code. Details are in
`docs/agent-instructions/epics-and-stories.md`.

## Step 4: close the loop

- Set the epic to `lifecycle: completed`.
- Set the feature to `active` in both its file and its index row.
- If implementation showed a requirement's text to be wrong, write a **new amendment** revising it.
- If it showed a decision to be wrong, write a superseding decision record, then the amendment.
