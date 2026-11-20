# spec-first

This repository is an **example of developing a software solution specification first**. The specification is
written and approved before the code, and both live here. The solution itself has not been defined yet:
`specification/` holds the process scaffolding with an empty corpus, and `src/` holds no code.

Start with [`docs/agent-instructions/README.md`](docs/agent-instructions/README.md). It holds the rules that
always apply and routes each kind of task to one short instruction file. The three procedures that are run
rather than read are skills in `.claude/skills/`:

| Skill | Use it when |
|---|---|
| `refine-change` | The user proposes, discusses, or asks to build any change. This is the default path for all work |
| `extract-requirements` | Code exists and no baseline does. Not the case here today |
| `consolidate-requirements` | The user asks to compact the requirements corpus |

## Project context

The skills hold no project details; they read them from here. Nothing is decided yet. When the user decides
one, replace the entry, and record it where the last column says.

| Item | Value | Also recorded in |
|---|---|---|
| What the software is | Not decided | The first feature file |
| Language, framework, and stack | Not decided | A decision record |
| Architectural style and layering | Not decided | A decision record |
| Feature flag platform and naming convention | Not decided | `specification/epics/README.md` and the decisions log in `specification/requirements/README.md` |
| Where tests live and how they are run | Not decided | `specification/epics/README.md` |

## Layout: the root groups files by purpose

| Path | Purpose |
|---|---|
| `specification/features/` | WHY, business. One file per feature (`FEAT-XXXX`), plus the template and index |
| `specification/decisions/` | WHY, technical. One architecture decision record per decision (`ADR-NNNN`), plus the template and index |
| `specification/requirements/` | WHAT. Append-only corpus (`REQ-NNNN`); `templates/` holds the block and document templates |
| `specification/epics/` | HOW. One folder per epic, stories inside; plus the two templates |
| `src/` | The software. Changes only by executing a `ready` story |
| `docs/getting-started.md` | Walkthrough of a first change, for a person new to the repository |
| `docs/process/` | Guides that span the workflow: end-to-end, adoption, consolidation, concurrent branches |
| `docs/agent-instructions/` | Operational digest of the guides, one file per task |
| `docs/examples/tideline/` | Worked example for a fictional store, mirroring the `specification/` layout |
| `.claude/skills/` | The executable procedures: `refine-change`, `extract-requirements`, `consolidate-requirements` |

Keep this grouping. A new file goes in the folder that matches its purpose; nothing but `README.md`,
`CLAUDE.md`, and tool configuration sits at the root.

## Rules for working here

1. **No code without a story.** Anything under `src/` is written by executing a story whose epic a human has
   moved to `ready`. A request to "just build X" starts the `refine-change` skill; it does not start with code.
2. **Every requirement has one of three sources.** `ratified` by citing a feature file, `ratified` by citing
   an accepted decision record, or `extracted` from existing software and citing neither. Nothing is
   `ratified` on the strength of a conversation alone. There is no `specs/` directory and no other kind of
   design document; a technical rationale is a decision record.
3. **The corpus is append-only, and accepted decisions are immutable.** Once a document in
   `specification/requirements/` is committed, it is never edited; an accepted decision record is changed
   only by superseding it. The guides, templates, skills, and READMEs are ordinary documents and are edited in place.
4. **Tideline is not this project.** `docs/examples/tideline/` is illustration. Never treat its features, decisions,
   requirements, IDs, or domain tags as part of this repository's specification, and do not copy them into
   it. Its links to `src/`, `db/`, and `test/` point at code that does not exist and are not
   broken links.
5. **Do not invent the product.** What this repository builds is the user's decision. Until they describe it,
   the domain tag table, feature index, decision index, and corpus stay empty.
6. **Project details go in Project context above, not in the skills.** The skills stay free of anything
   specific to one product, so they can be copied to another repository unchanged.
7. **Relative links must resolve.** After renaming or moving a file, grep for its old name.
8. **Documentation must be findable from the root.** A new guide gets a row in the documentation table of
   `README.md` and in `docs/README.md` in the same change.

## Authority, and content that is repeated

The directory READMEs under `specification/` and the guides under `docs/process/` are authoritative.
`docs/agent-instructions/` is derived from them: if the two disagree, the guide wins and the digest is fixed.

The same content deliberately appears in several files. Change every place in the same change:

| Content | Appears in |
|---|---|
| The three requirement sources and the `ratified` rule | `README.md`, `specification/requirements/README.md`, `specification/decisions/README.md`, `specification/features/README.md`, the `refine-change` and `extract-requirements` skills, `docs/process/end-to-end-workflow.md`, `docs/agent-instructions/README.md` and `requirements.md` |
| Decision record fields, status, immutability | `specification/decisions/README.md`, `decision-template.md`, `docs/examples/tideline/specification/decisions/`, `docs/agent-instructions/decisions.md` |
| Requirement block shape and field rules | `specification/requirements/README.md`, `specification/requirements/templates/` (block and consolidation), `docs/examples/tideline/specification/requirements/`, `docs/agent-instructions/requirements.md` |
| Type tags | `specification/requirements/README.md`, `docs/agent-instructions/requirements.md` |
| Refinement steps and gates | `docs/process/end-to-end-workflow.md` (rationale), `.claude/skills/refine-change/SKILL.md` (procedure), `docs/getting-started.md` and `README.md` (summary) |
| Consolidation procedure and invariants | `docs/process/requirements-consolidation.md`, `.claude/skills/consolidate-requirements/SKILL.md`, `docs/agent-instructions/consolidation.md` |
| Epic and story fields, lifecycle | `specification/epics/README.md`, its two templates, `docs/examples/tideline/specification/epics/`, `docs/agent-instructions/epics-and-stories.md` |
| Validator checks | `docs/process/adopting-in-a-new-repo.md` §6, `docs/process/concurrent-branches-and-id-collisions.md`, `docs/agent-instructions/id-allocation.md` |
| The repository layout | `README.md`, this file, `specification/README.md`, `docs/README.md`, `docs/process/adopting-in-a-new-repo.md` §2, `docs/agent-instructions/README.md` |
| The list of skills | `README.md`, this file, `.claude/skills/README.md`, `docs/README.md`, `docs/agent-instructions/README.md`, `docs/process/adopting-in-a-new-repo.md` §3 |

## Open points in the process

Places where the guides disagree or leave something undefined. The digest's handling is noted; the guides have
not been changed.

| # | Open point | How the digest handles it |
|---|---|---|
| 1 | The concurrent-branches guide recommends a `specification/requirements/next-req-id` counter file, placeholder `REQ-????` IDs, and reading open merge requests in pre-flight. The requirements README, the workflow guide, and the templates do not mention them (the `refine-change` skill applies them), and the counter file does not exist here | Counter file is the allocation authority **if it exists**; otherwise the newest document's `next-req-id`. Late allocation is always applied |
| 2 | The adoption guide has the validator fail on any gap in the ID sequence; the concurrent-branches guide allows gaps listed in a known-skips file whose name and location are never defined | Gaps need a recorded reason; the file is left to be defined |
| 3 | The features README says a feature file is created when implementation begins; the workflow creates it in Step 0, before requirements are drafted | Treated as the same moment: starting a refinement is the start of the work, and the file is created `in-progress` |
| 4 | No validator, link checker, or renumber script exists yet, though the guides describe them | Checks are done by hand against the list in `docs/agent-instructions/id-allocation.md` |
| 5 | Three structural decisions are unrecorded: flag platform and naming, consolidation trigger, who may move an epic to `ready` | Ask the user before the first epic and record the answers in the decisions log |
