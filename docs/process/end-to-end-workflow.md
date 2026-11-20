# End-to-End Workflow

This document ties the pieces together. There are two distinct motions: a one-time **bootstrap** that
establishes the baseline from an existing codebase, and a repeating **refinement loop** that turns each new
change into a feature or decision record, requirements, and an epic.

```
 BOOTSTRAP (once)                    REFINEMENT LOOP (every change)
 ────────────────                    ──────────────────────────────
 existing code                       natural-language discussion
          │                                     │
          ▼                                     ▼
 known features + decisions          Step 0  source          ──▶ features/<domain>/<name>.md   [gate]
 written up and approved                        │             and/or decisions/NNNN-<name>.md
          │                                     ▼
          ▼                          Step 1  requirements    ──▶ requirements/<ts>-<desc>.md   [gate]
 /extract-requirements skill                    │
          │                                     ▼
          ▼                          Step 2  planning        ──▶ epics/<date>-<name>/          [gate]
 requirements/<ts>-baseline.md                  │
                                                ▼
                                     Step 3  implement stories in src/, record Files Modified
                                                │
                                                ▼ (periodically)
                                     /consolidate-requirements

 All output paths are under specification/.
```

## Part 1: Bootstrap a baseline

Run once, when adopting the process on a codebase that already exists.

1. **Inventory evidence.** List configuration files, architecture notes, entry points (controllers, handlers,
   jobs), filters and middleware, security code, recent migrations, and infrastructure definitions. The extraction
   skill proposes this list and asks you to correct it.
2. **Decide the domain tag table** from the code's module structure. Write it into
   `specification/requirements/README.md`.
3. **Write up what is already known to be deliberate.** A requirement can only be `ratified` by citing a
   feature file or an accepted decision record, and the baseline cannot be edited afterwards, so anything you
   want ratified in the baseline must exist first:
   - a [decision record](../../specification/decisions/README.md) for each architectural decision the team
     can vouch for, especially ones that look accidental in the code;
   - a [feature file](../../specification/features/README.md) with `status: active` for each live capability
     whose business value someone can state.

   Each needs human approval. Do as many or as few as are worth the effort; everything else is extracted, and
   can be ratified later by an amendment.
4. **Run the [`extract-requirements`](../../.claude/skills/extract-requirements/SKILL.md) skill**
   (`/extract-requirements` in Claude Code) with a capable model that has repository access. It first agrees
   its inputs with you: the system description, the source file list, the document outline, and the features
   and decision records from step 3.
5. **Review the output.** Pay particular attention to:
   - every `ratified` requirement cites a feature or decision that really does justify it;
   - every requirement citing neither is `extracted`;
   - no planned or partially implemented work has leaked in;
   - `parents` links resolve and the hierarchy is sensible;
   - the metadata block's `requirement-count` and `next-req-id` are correct.
6. **Commit the baseline** as `specification/requirements/YYYYMMDDHHNN-baseline-requirements.md`. From this
   moment the file is read-only.

Expect most of a first baseline to be `extracted`. That is the honest result: it shows how much of the system
nobody has yet written a reason for.

If there is no existing code, skip the bootstrap. The first refinement conversation produces the baseline
instead of an amendment: name it `-baseline-requirements.md`, set `type: baseline`, and start at `REQ-0001`.
Every requirement in it is `ratified`.

## Part 2: The refinement loop

Driven by the [`refine-change`](../../.claude/skills/refine-change/SKILL.md) skill, which Claude Code loads
when a change is proposed (or on `/refine-change`). The skill is written for an AI agent acting as a
skeptical architect, but a human facilitator can follow the same steps.

### Pre-flight: load context

Before any discussion, the facilitator reads:

1. The baseline, then every amendment in filename order (or the latest consolidation and the amendments after
   it). Note the `next-req-id`.
2. `specification/features/feature-index.md` and `specification/decisions/decision-index.md`, and the accepted
   decision records that touch the domains under discussion.
3. The four directory READMEs and the templates.

### Step 0: Establish the source

A requirement is `ratified` only by citing a feature file or an accepted decision record, so the source is
established before any requirement is written. Ask: *is this change in service of a business capability, a
technical decision, or both?*

| Branch | Action | Gate |
|---|---|---|
| **A. New feature** | Elicit business value, description, success criteria, scope, assumptions. Assign the next FEAT ID. Draft the feature file and index row. | User confirms the feature file before Step 1 |
| **B. Existing feature** | Identify the feature. Discuss what changes. Edit the feature file in place. | User confirms the updated file before Step 1 |
| **C. New decision** | Infrastructure, security hardening, performance, observability, patterns, technology. Elicit context, options, decision, consequences. Assign the next ADR ID. Draft the record as `proposed` with its index row. | User accepts the record; it becomes `accepted` before Step 1 |
| **D. Existing decision** | Identify the accepted record and confirm the change conforms to it. If the decision itself is changing, take branch C with a superseding record instead. | None beyond confirming the record applies |

A change may take more than one branch, and must take at least one. There is no path to Step 1 without a
feature or a decision.

### Step 1: Requirements refinement

Turn the discussion into requirements. Probe for both functional and non-functional concerns. Do not consider
Step 1 complete until these have been explicitly asked:

- **Security.** Does this touch authentication, authorization, or data access?
- **Performance and scalability.** Latency, throughput, caching?
- **Observability.** What logs, metrics, or traces are needed to operate it?
- **Backward compatibility.** Does this break an API contract, a cached structure, or a client?

Then draft a new amendment document:

- Filename `YYYYMMDDHHNN-<kebab-description>.md`.
- IDs continue from the pre-flight `next-req-id`.
- Every requirement is `ratified`.
- Every requirement cites its source: `features` where Step 0 took branch A or B, `decisions` where it took
  branch C or D, both where both apply.

### Gate: Step 1 → Step 2

1. **Present the complete draft requirement set** in final formatted form.
2. **Check for conflicts and dependencies** against the loaded corpus. Call out explicitly:
   - existing requirements **superseded or retired** by the change (re-emit them with `status: retired` and
     `retired-by`);
   - existing requirements the change **depends on** (reference in `parents`);
   - existing requirements the change **conflicts with** (must be resolved before proceeding);
   - existing requirements citing a decision record **superseded** in Step 0 (revise or retire them).
3. **Ask for explicit approval**: *"Here are the N requirements I've drafted, and M existing requirements
   that are affected. Do you approve these as written, or are corrections needed before implementation
   planning?"*

Nothing in Step 2 begins until the user says yes.

### Step 2: Implementation planning

1. **Define the feature flag**: name, default state, removal criteria. Record it in the epic README.
2. **Create the epic** folder and README with `lifecycle: draft`, linking the parent requirements, features,
   and decisions.
3. **Decompose into stories** of 0–3 points, ordered so each is independently mergeable. Write acceptance
   criteria and implementation notes concrete enough for an agent with no conversational memory.
4. **Human review** moves the epic and stories from `draft` to `ready`.

### Step 3: Implementation

Each story is executed in turn (see [specification/epics/README.md](../../specification/epics/README.md#executing-a-story)).
A useful working rule: one story per commit, with the story file's lifecycle change and Files Modified in the
same commit as the code.

### Step 4: Close the loop

When the epic completes:

- Set the epic's `lifecycle: completed`.
- Update the feature file's status (`in-progress` → `active`) and index row.
- If implementation revealed that a requirement's text was wrong, write a **new amendment** revising it. Never
  edit the earlier amendment.
- If implementation revealed that a decision was wrong, write a **superseding decision record**, then an
  amendment for the requirements that cited the old one. Never edit an accepted record.

## Part 3: Periodic consolidation

As amendments accumulate, the pre-flight read gets long. When the corpus crosses a threshold you choose (a
number of amendments, a total size, or a release boundary), run the
[`consolidate-requirements`](../../.claude/skills/consolidate-requirements/SKILL.md) skill. It produces a single
consolidation document that becomes the new starting point for reads, moves the folded documents to
`specification/requirements/archive/`, and changes no requirement's meaning. Details in
[docs/process/requirements-consolidation.md](requirements-consolidation.md).
