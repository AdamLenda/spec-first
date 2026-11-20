---

#### Epic: kebab-case-name

| Field | Value |
|---|---|
| name | `kebab-case-name` | <!-- Short machine-readable identifier -->
| refined-by | `@handle` | <!-- Who refined this epic -->
| lifecycle | `draft` | <!-- draft, ready, in-progress, completed, abandoned -->
| parent-requirements | REQ-0001 · REQ-0002 | <!-- Requirement IDs. Use · as separator. — if none -->
| features | FEAT-0001 | <!-- Feature IDs. Use · as separator. — if none -->
| decisions | ADR-0001 | <!-- Decision record IDs. Use · as separator. — if none -->
| feature-flags | `flag-name` | <!-- Flag names. Use · as separator. — if none (explain in Constraints) -->
| references | [Decision](../../decisions/NNNN-decision-name.md) · [Design](https://example.org/design) | <!-- Markdown links with · separator -->

---

## Goal

<!-- One or two sentences: what problem does this epic solve? -->

## Scope

**Included:**
- ...

**Not included:**
- ...

## Constraints

<!-- Limitations, dependencies, assumptions, story sequencing rules, feature flag default and removal criteria. -->

- Feature flag `flag-name` defaults **off** in production. Removal criteria: ...

## Stories

- [01 — story-name](01-story-name.md)
- [02 — story-name](02-story-name.md)
