---
nvb: plan
initiative: {{N-xxx-slug}}
version: 1
status: draft
approved_by: ""
approved_on: ""
covers: [triage@{{n}}, prd@{{n}}, research@{{n}}, prototype@{{n}}, design@{{n}}, system@{{N}}]   # only those that exist
system_sections: [{{architecture, data-model}}]   # copied from the design (R7 staleness)
adrs: [{{ADR-0003}}]
---

# {{N-xxx}} — {{title}} · Plan

<!-- guide: The plan is approved, and then never edited to follow progress. Progress lives in the task files. -->

## Approach | Approche
<!-- nvb:approach mandatory -->
<!-- guide: 3 to 6 lines: the order of the work and why. Which interface is frozen first. -->

## Tasks | Tâches
<!-- nvb:tasks mandatory -->
<!-- guide: Ordered list. Every task meets the four "bounded task" conditions (a–d) of nvb-plan. Scope = files or components announced, as paths or globs. Type "technical" requires a justification. Size is an estimate (lines / files). Above ~300 lines or 5 files, a split question is asked. -->

| ID | Title | Observable outcome | Implements | Depends on | Scope | Type | Size |
|---|---|---|---|---|---|---|---|
| T-01 | {{…}} | {{one result, checked by a test or a command}} | {{DES-01, REQ-02}} | — | {{src/auth/**}} | feature | {{~120 l / 3 f}} |
| T-02 | {{…}} | {{…}} | {{…}} | T-01 | {{…}} | technical: {{why}} | {{…}} |

## Shared interfaces | Interfaces partagées
<!-- nvb:shared-interfaces mandatory -->
<!-- guide: Every interface that two tasks or more depend on is frozen by a dedicated task, and the other tasks depend on it. "None." if no interface is shared. -->

## Coverage | Couverture
<!-- nvb:coverage mandatory -->
<!-- guide: R6. One row per REQ and SEC (realised AND verified, possibly by different tasks), one row per DES, one row per OPS. An OPS not taken by a task is "Handed over" with the recipient and the place where it is recorded. -->

| Upstream ID | Realised by | Verified by | Note |
|---|---|---|---|
| REQ-01 | T-02 | T-02 | {{criterion that verifies it}} |
| SEC-01 | T-03 | T-04 | {{…}} |
| DES-01 | T-01 | T-01 | |
| OPS-01 | — | — | Handed over to {{Deploy owner}}, {{where}} |

## Adversarial review | Revue adverse
<!-- nvb:adversarial-review mandatory -->
<!-- guide: Findings of the independent review (partial realisation, task without test, circular dependency, overlapping scopes in parallel tasks, OPS forgotten, task citing nothing) and what became of each: changed (task ID), open question (Q-xx), or rejected by the person (their reason). -->

## Open questions | Questions ouvertes
<!-- nvb:open-questions mandatory -->
None.

## Change log | Historique
<!-- nvb:changelog mandatory -->

| Version | Date | Added | Modified | Removed | Note |
|---|---|---|---|---|---|
| 1 | {{YYYY-MM-DD}} | {{T-01…}} | — | — | Initial version |
