---
nvb: task
initiative: {{N-xxx-slug}}
id: T-{{xx}}
title: "{{…}}"
derived_from: plan@{{n}}          # the approved plan; this file has no approval of its own
covers: [{{prd@2, design@1, system@4}}]   # fingerprints of the sources quoted below (R7)
system_sections: [{{architecture}}]        # system sections this task reads, subset of the design's (R7)
adrs: [{{ADR-0003}}]
implements: [{{REQ-01, SEC-02, DES-03}}]  # copied from the plan, never extended here
depends_on: [{{T-01}}]
scope: [{{src/auth/**, tests/auth/**}}]
type: {{feature|technical}}
state: todo                       # todo | in-progress | verified | done | reclassify | removed
                                  # the agent writes: in-progress, verified, reclassify
                                  # nvb-plan writes: todo, removed (task dropped by a new plan version)
                                  # only the person writes: done
---

# {{N-xxx}} / T-{{xx}} — {{title}}

<!-- guide: This file is the brief of the Build agent. Content comes only from the approved plan and the sources it cites. A task file adds no requirement, decision or scope. If something is missing, the plan must change (new version), not this file. Every section is mandatory. -->

## Objective | Objectif
<!-- nvb:objective mandatory -->
<!-- guide: The single observable outcome, copied from the plan. -->

## Context | Contexte
<!-- nvb:context mandatory -->
<!-- guide: 3 to 5 lines: what the initiative builds and where this task fits. Quote every triage derogation verbatim. -->

## Implements | Réalise
<!-- nvb:implements mandatory -->
<!-- guide: One line per ID, with its text quoted from the source (PRD acceptance criterion, DES text). The contract with the Build phase: a change that serves no ID listed here is out of design. -->

- **REQ-01** — "{{quoted}}" (prd@{{2}})

## Read first | À lire
<!-- nvb:read mandatory -->
<!-- guide: Precise sections, not whole documents: file#heading plus the IDs to look at. ADRs: the Decision and Consequences sections. Conventions: CLAUDE.md / CONVENTIONS.md of the repository, if they exist (the nvb skills do not write them). -->

- `docs/nvb/{{N-xxx-slug}}/design.md#interfaces` — DES-03
- `docs/nvb/system/decisions/ADR-{{xxxx}}-….md` — Decision, Consequences

## Scope | Périmètre
<!-- nvb:scope mandatory -->
<!-- guide: Files and components that may change (same as front matter `scope`), then explicitly out of scope. A diff that leaves this scope does not become "verified": it becomes "reclassify" (Build check). -->

- **In:** …
- **Out:** …

## Success criteria | Critères de réussite
<!-- nvb:success mandatory -->
<!-- guide: Executable. Each criterion is a command or a test name, with the expected result. At least one criterion per implemented REQ/SEC that this task verifies (plan coverage table). -->

- [ ] `{{command}}` → {{expected result}}

## Avoid | À éviter
<!-- nvb:avoid mandatory -->
<!-- guide: Prohibitions quoted from the design, ADR consequences, SEC requirements and the prototype's "must not be carried over" list, each with its source ID. -->

- {{prohibition}} ({{ADR-0003 / SEC-02 / PRO-01}})

## Execution log | Journal d'exécution
<!-- nvb:execution -->
<!-- guide: Filled at Build, not at Plan. When the criteria pass, the agent sets state: verified and pastes the commands and outputs here. The person sets state: done after review. Only "done" unblocks the dependent tasks. -->
