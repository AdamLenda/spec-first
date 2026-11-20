# Bootstrap and Adoption

Setting the process up in a repository and extracting the baseline. Done once. Digest of
`docs/process/adopting-in-a-new-repo.md` and `docs/process/end-to-end-workflow.md` Part 1.

## 1. Settle the structural decisions with a human

Ask, do not assume. Record each answer in the decisions log in `specification/requirements/README.md`.

| Question | Default |
|---|---|
| Where the directories live | `specification/` at the repo root, holding `features/`, `decisions/`, `requirements/`, `epics/` |
| ID format | Flat sequential `REQ-NNNN` / `FEAT-XXXX` / `ADR-NNNN` (document-scoped if many branches author in parallel) |
| Domain tag table | Derived from top-level modules or packages |
| Requirement language | RFC 2119 |
| Feature flag platform and naming | Whatever the codebase already uses; define one before the first epic |
| Consolidation trigger | Every N amendments, or each release |
| Who may move an epic `draft` → `ready` | Any reviewer other than the author |

## 2. Create the directories

Copy the `specification/` tree from the reference repository:

```
specification/
  README.md
  features/        README.md, feature-template.md, feature-index.md (empty)
  decisions/       README.md, decision-template.md, decision-index.md (empty)
  requirements/    README.md, templates/
  epics/           README.md, epic-readme-template.md, story-template.md
```

Then, in `specification/requirements/README.md`, replace the domain tag table and the decisions log with the
repository's own. Do not copy `docs/examples/`.

## 3. Install skills and instructions

- Copy `.claude/skills/` (`refine-change`, `extract-requirements`, `consolidate-requirements`). For a tool other than Claude Code, put each `SKILL.md` where the tool loads prompts from and adjust the frontmatter.
- Copy `docs/process/` and `docs/agent-instructions/`; skills and READMEs link to them.
- Write a **Project context** section in `CLAUDE.md` or `AGENTS.md`: stack, architectural style, flag
  convention. The skills read project details from there and hold none themselves.
- If the directories live somewhere other than `specification/`, correct every path in one pass.
- Add a short section to `CLAUDE.md` or `AGENTS.md` pointing at the process and stating the rules: never edit
  an existing requirements document; use the `refine-change` skill before planning a change; record Files
  Modified in each story.

## 4. Extract the baseline

Skip this if there is no existing code; the first refinement writes the baseline.

1. **Inventory evidence:** configuration, architecture notes, entry points (controllers, handlers, jobs),
   middleware and filters, security code, the most recent 10–15 migrations, infrastructure definitions. The
   extraction skill confirms this list with the user before reading.
2. **Write the domain tag table** into `specification/requirements/README.md`.
3. **Write up what is known to be deliberate, before extracting.** The baseline is read-only once committed,
   so anything to be `ratified` in it needs its source first. With a human who knows the system:
   - a decision record for each architectural decision they can vouch for ([decisions.md](decisions.md));
   - a feature file with `status: active` for each live capability whose business value they can state.

   Each needs explicit approval. Do not invent either from reading the code; that is extraction.
4. **Run** the `extract-requirements` skill. Read every listed source before writing
   any requirement.
5. **Apply the extraction rules:**
   - `ratified` only where the requirement cites a feature file or `accepted` decision record that exists
     now and the code implements the behaviour. Everything else is `extracted` with `—` in both fields;
   - do not create features or decisions during extraction. Where a deliberate decision seems to be missing
     its record, mark the requirement `extracted` and say so in an assumption callout;
   - no planned work, no unimplemented portions, no test infrastructure, build tooling, or developer workflow;
   - every block has `revision: 1` and today's date;
   - IDs sequential from `REQ-0001` with no gaps;
   - sections mirror the domain tag table and are navigation only; hierarchy is in `parents`;
   - invent nothing. Flag inferences with an assumption callout.
6. **Review with a human who knows the system.** Check that every `ratified` requirement is really justified
   by what it cites, no planned work leaked in, `parents` links resolve, and `requirement-count` and
   `next-req-id` are right. Expect most of the baseline to be `extracted`.
7. **Commit** as `specification/requirements/YYYYMMDDHHNN-baseline-requirements.md`. From then on it is
   read-only. Later ratification is by amendment.

## 5. Add guardrails

- A **validator** in CI or pre-commit (checks listed in [id-allocation.md](id-allocation.md)).
- A **link checker** for `references` paths.
- An **index check**: every feature file and decision record has an index row and every row has a file.
- The `specification/requirements/next-req-id` counter file, if the flat ID scheme is used.

## 6. Run one real change through the loop

Pick a small, real change and take it through the `refine-change` skill end to end. Write the
conventions it surfaces (flag naming, where tests live, how migrations are numbered) into `specification/epics/README.md`.

## Mistakes to avoid

- Feature files for planned work.
- Marking a requirement `ratified` without a feature or decision to cite.
- Editing an accepted decision record.
- Editing an amendment after it merged.
- Expressing hierarchy through headings instead of `parents`.
- Stories that need the planning conversation to be understood.
- Drafting an epic before the requirements are approved.
- Leaving Files Modified empty.
