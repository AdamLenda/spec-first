# Adopting the Process in a New Repository

A checklist for standing up the directories, templates, and conventions. Budget an afternoon for an existing
codebase of moderate size, most of it reviewing the extracted baseline.

## 1. Decide the structural questions up front

Record each answer in `specification/requirements/README.md` under a Decisions Log so they are not revisited casually.

| Question | Default answer | Alternatives to consider |
|---|---|---|
| Where do the directories live? | `features/`, `decisions/`, `requirements/`, and `epics/` under `specification/` at the repo root, beside `src/` and `docs/` | Directly at the repo root, or a separate repository if several services share one corpus |
| ID format | `REQ-NNNN` / `FEAT-XXXX` / `ADR-NNNN`, flat sequential | Document-scoped IDs if many branches author in parallel (see [concurrent branches](concurrent-branches-and-id-collisions.md)) |
| Domain tag table | Derived from top-level modules or packages | Derived from bounded contexts if the code layout does not reflect the domain |
| Requirement language | RFC 2119 | EARS syntax, if the team prefers templated sentences |
| Feature flag platform and naming convention | Whatever the codebase already uses | Define one before the first epic if none exists |
| Consolidation trigger | Every N amendments or each release | Size-based, or on demand |
| Who may move an epic from `draft` to `ready` | Any reviewer other than the author | A named owner per domain |

If you choose a different location, every path in the READMEs, skills, and agent instructions
changes with it. Do that as one search-and-replace before anything else.

## 2. Create the directories

Copy this repository's `specification/` tree. It is laid out exactly as an adopting repository needs it:

```
specification/
  README.md                 ← map of the directories
  features/
    README.md               ← the feature guide; keep as is
    feature-template.md
    feature-index.md        ← empty registry
  decisions/
    README.md               ← the decision record guide; keep as is
    decision-template.md
    decision-index.md       ← empty registry
  requirements/
    README.md               ← the requirements guide; replace the domain tag table and decisions log with YOURS
    templates/              ← requirement block, baseline, amendment, and consolidation templates
  epics/
    README.md               ← the epics guide; add your flag and test conventions
    epic-readme-template.md
    story-template.md
```

Do not copy `docs/examples/`; it is the fictional Tideline illustration. Each README cross-references the
others so a reader landing anywhere finds the whole picture.

## 3. Install the skills

- Copy `.claude/skills/`. It holds the three procedures: `refine-change`, `extract-requirements`, and
  `consolidate-requirements`. Claude Code loads them from there. Each `SKILL.md` is plain Markdown, so for
  another tool, put the files wherever it loads prompts or commands from and adjust the frontmatter.
- Copy `docs/process/`, which the skills and READMEs link to for rationale, and `docs/agent-instructions/`.
- The skills hold no project details. Write the stack, architectural style, and flag convention into a
  **Project context** section of the agent instructions file (next step); the skills read it from there.

## 4. Tell the agent about the process

Add a short section to the repository's agent instructions file (`CLAUDE.md`, `AGENTS.md`, or equivalent):

```markdown
## Specification-first workflow
Business value lives in `specification/features/` (FEAT-XXXX), technical decisions in
`specification/decisions/` (ADR-NNNN), normative requirements in `specification/requirements/` (REQ-NNNN,
append-only baseline + amendments), and implementation plans in `specification/epics/`. A requirement is
`ratified` only if it cites a feature or an accepted decision; otherwise it is `extracted`. Before planning any change, use the `refine-change` skill
and follow its steps. Never edit an existing requirements document; write a new amendment. Record Files
Modified in each story after implementing it. Task-by-task rules are in `docs/agent-instructions/`.
```

## 5. Bootstrap the baseline

Follow [docs/process/end-to-end-workflow.md, Part 1](end-to-end-workflow.md#part-1-bootstrap-a-baseline). Review
the result with at least one person who knows the system well. Expect most
requirements to be `extracted`, and expect to split a few over-broad ones.

## 6. Add guardrails

Lightweight automation pays for itself quickly:

- **A validator** (CI step or pre-commit hook) that parses every requirements document and fails on duplicate
  IDs, gaps in the sequence, unresolved `parents` or `retired-by` references, unknown status or tag values,
  `next-req-id` disagreeing with the highest ID present, edits to a document that is not the newest one, a
  `ratified` requirement citing no feature or decision, an `extracted` one citing either, or a cited decision
  that is not `accepted`.
- **A link checker** for `references` paths.
- **An index check** that every feature file and decision record has a row in its index and every row has a
  file.

See [docs/process/concurrent-branches-and-id-collisions.md](concurrent-branches-and-id-collisions.md) for the
validator's role in collision detection.

## 7. Run the first refinement

Pick a small, real change and walk the whole loop end to end. The first pass surfaces missing conventions
(flag naming, where tests live, how migrations are numbered) that should be written into the epics README
before anyone else uses the process.

## Common mistakes

- **Creating feature files for planned work.** Features exist when work begins. Use issues for ideas.
- **Marking a requirement `ratified` because everyone agrees with it.** It is ratified when it cites a feature
  or an accepted decision. Write the record.
- **Editing an accepted decision record.** Write a superseding one.
- **Editing an amendment after it is merged.** Write a new one. The validator should block this.
- **Letting hierarchy live in headings.** `parents` is the hierarchy; headings are navigation.
- **Stories that need the planning conversation to understand.** Implementation Notes must stand alone.
- **Skipping the gate.** Drafting an epic before the requirements are approved produces plans for the wrong
  thing and burns the approval conversation on implementation detail.
- **Forgetting Files Modified.** Without it the audit trail from requirement to code is broken.
