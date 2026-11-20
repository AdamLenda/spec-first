# Amendment: <Short Title>

| Field | Value |
|---|---|
| document-id | `YYYYMMDDHHNN-kebab-case-description` |
| type | `amendment` |
| created | YYYY-MM-DD |
| created-by | <name> |
| requirement-count | [N] new · [M] revised · [K] retired |
| next-req-id | REQ-[first unused ID after this document] |
| supersedes | — |
| amended-by | — |

<!--
One or two paragraphs: what change this amendment captures, which feature (FEAT-XXXX) it relates to if any,
or which decision record (ADR-NNNN), and a pointer to that file. Every requirement added by an amendment is
`ratified` and cites at least one feature or accepted decision.

If this amendment revises earlier requirements, add a dated note summarising why, e.g.:

> **Revision note (YYYY-MM-DD):** After the first production deployment the data shape changed. REQ-0121 and
> REQ-0123 are revised to revision 2; the revision 1 text is superseded.
-->

See [`specification/features/<domain>/<feature>.md`](../features/<domain>/<feature>.md) for the business-facing description.

---

## New requirements

<!-- Full requirement blocks for each new REQ-NNNN, continuing the global sequence. -->

## Revised requirements

<!--
Full re-emitted blocks for any existing requirement this amendment changes. Increment `revision`, update
`revised`. The re-emitted block is the complete current text; readers never diff against the earlier version.
-->

## Retired requirements

<!--
Full re-emitted blocks with `status: retired` and a `retired-by` row pointing at the superseding REQ-NNNN
(or `—` if the behaviour is removed outright). Keep the original normative text so the audit trail is complete.
-->
