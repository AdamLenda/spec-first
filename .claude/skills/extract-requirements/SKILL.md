---
name: extract-requirements
description: Extract a baseline requirements document from an existing codebase that has no requirements corpus yet. Use once per repository, when the user asks to bootstrap, extract, or reverse-engineer requirements from code that already exists. Not for new changes; those use refine-change.
---

# Extract baseline requirements

Read an existing codebase and produce `specification/requirements/<YYYYMMDDHHNN>-baseline-requirements.md`.
The baseline is permanent: once committed it is never edited, so a human reviews it before it is committed.

Act as a veteran software architect performing a thorough, skeptical extraction. Be explicit about what is
an inference and what is supported by a feature file or decision record. Do not invent requirements that the
source material does not evidence.

The rationale is in `docs/process/end-to-end-workflow.md` Part 1.

## When not to run this

- **A baseline already exists.** Changes are amendments; use `refine-change`.
- **There is no code.** The first `refine-change` conversation writes the baseline.

## 1. Establish the inputs

Agree these with the user before reading any code. Present each as a proposal for them to correct.

1. **What the system is.** Two to six bullets: the product, its users, its major capabilities, its runtime
   shape, its principal external integrations. Start from `CLAUDE.md` Project context and the repository's
   own README.
2. **The domain tag table.** One `domain:` tag per top-level module or bounded context, in
   `specification/requirements/README.md`. If the table is empty, propose one from the module structure and
   write it there once confirmed.
3. **What is already known to be deliberate.** Read `specification/features/feature-index.md` and every
   feature file with status `active` or `in-progress`. Read `specification/decisions/decision-index.md` and
   every `accepted` decision record. These are the only things a `ratified` requirement may cite.

   Ask the user whether any architectural decisions or live capabilities they can vouch for are still
   unwritten. If so, stop: those are written and approved first (see `docs/agent-instructions/decisions.md`
   and `features.md`), because the baseline cannot be edited afterwards. Do not write them yourself from
   reading the code; that is extraction.
4. **The source list.** Survey the repository and propose the files to read, grouped as:
   - configuration;
   - architecture documentation;
   - entry points (controllers, handlers, CLI, jobs);
   - middleware, filters, interceptors;
   - security;
   - domain services;
   - the most recent 10–15 database migrations;
   - infrastructure definitions.
5. **The document outline.** Numbered sections that mirror the domain tag table. Sections are for human
   navigation only.

## 2. Read

Read every file on the agreed source list before writing any requirement. Read
`specification/requirements/README.md` for the field rules, status vocabulary, and tag taxonomy.

## 3. Write the requirements

Start the document from `specification/requirements/templates/baseline-document-template.md`. Write every
requirement from `specification/requirements/templates/requirement-block-template.md`, exactly: the blocks
are machine-parsed, so do not change field names, table structure, or formatting.

In a baseline, `revision` is always `1` and `revised` is today's date. `references` are Markdown links with
paths relative to the document (`../../src/...`), separated by ` · `.

### Status follows from the source

| Source | Status | Fields |
|---|---|---|
| A business capability described in an existing feature file | `ratified` | `features` cites it |
| A technical decision in an existing `accepted` decision record | `ratified` | `decisions` cites it |
| The existing software alone | `extracted` | `features` and `decisions` are both `—` |

A requirement is `ratified` if and only if it cites a feature file or accepted decision record that exists in
the repository now **and** the code implements the behaviour. Everything else is `extracted`, however
deliberate it looks. Never cite a feature or decision that does not exist.

If behaviour looks like a deliberate architectural decision and no record covers it, mark the requirement
`extracted` and add an assumption callout naming the decision that appears to be missing. A human decides
whether to write the record.

### Granularity

1. **Bias toward specificity.** A high-level `feature` requirement is the parent of several detailed
   `functional` requirements.
2. **Hierarchy is in `parents`,** not in section nesting.
3. **One behaviour per requirement.**
4. **Cross-cutting requirements get several domain tags.**

Too coarse:

> The system SHALL provide a complete checkout experience including cart management, payment processing,
> order recording, and fulfilment.

A high-level feature requirement, tagged `feature` `application` `domain:checkout`:

> The system SHALL provide a checkout flow that allows a customer to purchase the contents of their cart
> using the configured payment provider.

One of its detailed children, tagged `functional` `integration` `application` `domain:checkout`
`domain:payments`, with the requirement above in `parents`:

> The system SHALL create a hosted payment session with the payment provider when a customer initiates
> checkout and SHALL redirect the customer to it with HTTP 303.

### Exclude

1. **Planned or aspirational work.** Work that has not begun gets no ID.
2. **The unimplemented part of partial work.** Document only what is implemented.
3. **Test infrastructure.** Frameworks, containers, test-only configuration.
4. **Build tooling.** Build tasks, asset pipelines, image builds.
5. **Developer workflow.** Git conventions, lint rules, commit message formats.

### Assumptions

Where a statement is not directly evidenced, add a callout immediately after the requirement:

```
> ⚠️ **Assumption:** [What is assumed, and what evidence is missing.]
```

## 4. Verify

- [ ] Every requirement uses the block template exactly
- [ ] Every requirement has at least one type tag and at least one domain tag (or `infrastructure`)
- [ ] IDs start at `REQ-0001`, are sequential with no gaps, and none is reused
- [ ] Every `parents` reference points to an ID in the document
- [ ] No planned or roadmap item is included
- [ ] Every `ratified` requirement cites at least one existing feature file or `accepted` decision record
- [ ] Every `extracted` requirement has `—` in both `features` and `decisions`
- [ ] Every cited `FEAT-XXXX` and `ADR-NNNN` exists in its index
- [ ] RFC 2119 language is used consistently
- [ ] Every `references` link is valid Markdown and its path exists
- [ ] The metadata `requirement-count` and `next-req-id` are accurate

## 5. Hand over for review

Do not commit. Report the counts of `ratified` and `extracted` requirements, list every assumption callout,
and name the apparent decisions that have no record. Expect most of a first baseline to be `extracted`: that
shows how much of the system nobody has yet written a reason for.
