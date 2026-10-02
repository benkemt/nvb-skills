---
nvb: task
initiative: {{N-xxx-slug}}
id: T-01
title: "{{…}}"
kind: {{bug-fix|small-feature}}
version: 1
status: draft                     # draft | approved, only the person writes approved (R2): no plan above this task
approved_by: ""
approved_on: ""
covers: [triage@{{n}}, research@{{n}}, prototype@{{n}}]   # only those that exist
implements: [{{triage, REQ-01, SEC-01}}]   # "triage" satisfies condition (d) of a bounded task
depends_on: []
scope: [{{src/…}}]
type: feature
state: todo                       # todo | in-progress | verified | done | reclassify | removed
                                  # the agent writes: in-progress, verified, reclassify
                                  # only the person writes: done
---

# {{N-xxx}} / T-01 — {{title}}

## Objective | Objectif
<!-- nvb:objective mandatory -->
<!-- guide: The single observable outcome. -->

## Context | Contexte
<!-- nvb:context mandatory -->
<!-- guide: Level and triage answers in two lines. Quote every derogation verbatim. -->

## Bug | Anomalie
<!-- nvb:bug -->
<!-- guide: Mandatory for kind: bug-fix, and removed for small-feature. All three items are required. -->

- **Expected behaviour:** {{…}}
- **Reproduction:** {{steps or command, with the observed result}}
- **Failing test:** `{{test name or command}}` fails before the fix and passes after it.

## Requirements | Exigences
<!-- nvb:requirements -->
<!-- guide: Mandatory for kind: small-feature. For bug-fix, only when the triage carries a TQ-S derogation. REQ-xx and SEC-xx with a testable acceptance criterion, as in the PRD template. At least one SEC-xx when a TQ-S question has a derogation. -->

- **REQ-01** — {{…}}. *Acceptance:* {{…}}.

## Read first | À lire
<!-- nvb:read mandatory -->
<!-- guide: The files and line ranges involved, each with one sentence saying what is there and why it matters, and RES/PRO if any. Conventions: CLAUDE.md / CONVENTIONS.md if they exist. -->

## Scope | Périmètre
<!-- nvb:scope mandatory -->

- **In:** …
- **Out:** …

## Success criteria | Critères de réussite
<!-- nvb:success mandatory -->
<!-- guide: Executable commands or test names, with the expected result. For a bug, the failing test is the first criterion. -->

- [ ] `{{command}}` → {{expected result}}

## Avoid | À éviter
<!-- nvb:avoid mandatory -->
<!-- guide: Prohibitions stated by the person, or from RES/PRO, each with its source. Write "None stated." if there is none. -->

## Coverage | Couverture
<!-- nvb:coverage mandatory -->
<!-- guide: R6. One row per REQ and SEC of this task (for a bug: the expected behaviour, as "triage"), with the success criterion that verifies it. Then the RES and PRO it relies on. -->

| Upstream ID | Verified by | Note |
|---|---|---|
| {{REQ-01}} | {{criterion 1}} | |

## Open questions | Questions ouvertes
<!-- nvb:open-questions mandatory -->
None.

## Execution log | Journal d'exécution
<!-- nvb:execution -->
<!-- guide: Filled at Build. Same rules as templates/task.md. -->

## Change log | Historique
<!-- nvb:changelog mandatory -->

| Version | Date | Added | Modified | Removed | Note |
|---|---|---|---|---|---|
| 1 | {{YYYY-MM-DD}} | {{REQ-01…}} | — | — | Initial version |
