# Features

A feature file states **why**: the business value of a set of changes. Digest of `specification/features/README.md`. Template:
`specification/features/feature-template.md`. Example: `docs/examples/tideline/specification/features/digital-delivery/license-key-delivery.md`.

## Before creating one

- Create a feature file only when work is starting. There is no `planned` status; if you want one, the file
  should not exist yet.
- Not every change has a feature. Cross-cutting or foundational work rests on a decision record instead
  ([decisions.md](decisions.md)); its requirements have `features: —` and cite the decision.
- A feature file is what ratifies the requirements that cite it, so it must be approved before they are
  written.

## Create

1. Take the next `FEAT-XXXX` from `specification/features/feature-index.md`.
2. Choose the domain subdirectory: kebab-case, matching a `domain:` tag. Create it if needed.
3. Copy the template to `specification/features/<domain>/<kebab-name>.md`. No date in the filename.
4. Add the index row **in the same change**. The shared index is what makes two branches adding features
   conflict at merge, which is intended.
5. Set `status: in-progress`.
6. Once requirements exist, set `features` on each requirement that implements it and list them under Related
   Requirements.

## Metadata

| Field | Value |
|---|---|
| `id` | `FEAT-XXXX` from the index |
| `name` | kebab-case, unique across the corpus |
| `status` | `in-progress` → `active` → `deprecated` → `removed` |
| `domain` | Matches a `domain:` tag |
| `depends-on` | FEAT IDs separated by ` · `, or `—` |
| `references` | Markdown links to epics, designs, decision records, separated by ` · `, or `—` |

`removed` features keep their file for audit.

## Sections and the test for each

| Section | Passes when |
|---|---|
| Business Value | Written from the user's or business's point of view: the pain, who has it, the observable improvement |
| Description | A few sentences on what becomes possible. No technical approach |
| Success Criteria | Checkboxes a non-engineer could verify by using the product or reading a metric |
| Scope | **Included** and **Not included** lists; every exclusion gives its reason |
| Assumptions | One `> ⚠️ **Assumption:**` callout per unconfirmed belief, or "None." |
| Related Requirements | `REQ-NNNN · one line` each. Informational; the requirement's `features` field is the authoritative link |

Technical detail belongs in requirements, epics, and stories, and technical reasoning in a decision record.
If a sentence names a table, class, or endpoint, move it.

## Update

Edit in place and update the index row in the same change. Features do not use the amendment model.

When a requirement stops contributing to a feature, retire it through a requirements amendment and then update
Related Requirements to match.

## Gate

Present the drafted or updated file and get explicit confirmation before starting requirements.
