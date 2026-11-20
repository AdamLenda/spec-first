# Worked Example: Tideline

Tideline is a **fictional** e-commerce platform that sells both physical goods (shipped by a carrier) and
digital goods (downloads and license keys). These files show what each artifact in the process looks like when
filled in, following one change end to end: replacing permanent download links with expiring links and adding
license-key delivery.

```
specification/features/digital-delivery/license-key-delivery.md            FEAT-0002: business why
specification/decisions/0003-idempotent-webhook-handling.md               ADR-0003: technical why
specification/requirements/202603101500-license-key-delivery.md            REQ-0120..0124 added, REQ-0057 retired: what
specification/epics/2026.03.10-license-key-delivery/README.md              the plan
specification/epics/2026.03.10-license-key-delivery/01-add-entitlement-table-and-repository.md   one story, completed
```

Only story 01 is written out; the epic README lists stories 02–07 to show how a full plan reads.
The files sit under `specification/` here exactly as they would in Tideline's own repository, so their
relative links to each other resolve. Links that leave the specification (`src/`, `db/`, `test/`)
point at Tideline code that is illustrative and does not exist.
