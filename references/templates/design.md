---
nvb: design
initiative: {{N-xxx-slug}}
version: 1
status: draft
approved_by: ""
approved_on: ""
covers: [triage@{{n}}, prd@{{n}}, research@{{n}}, prototype@{{n}}, system@{{N}}]   # only those that exist
system_sections: [{{architecture, data-model, security-model}}]   # system sections this delta reads or changes
touches: [{{architecture, data-model}}]                             # system sections this delta changes (subset)
adrs: [{{ADR-0003}}]                                                # ADRs this design relies on
---

# {{N-xxx}} — {{title}} · Design (delta against system@{{N}})

<!-- guide: This file describes only what changes compared with system@N. Do not restate the system: cite it (system/architecture.md#<heading>). Every DES-xx or OPS-xx is something a task can implement or verify. -->

## Overview | Vue d'ensemble
<!-- nvb:overview mandatory -->
<!-- guide: 5 to 10 lines, then one Mermaid diagram of the changed parts and their direct neighbours. Mark new elements as new. -->

```mermaid
flowchart LR
  {{…}}
```

## Architecture and components | Architecture et composants
<!-- nvb:architecture mandatory -->
<!-- guide: New or changed components, their responsibility, and their dependencies. Write "No change, because <reason>." if nothing changes. -->

- **DES-01** — {{component / change}}. *Responsibility:* … *Depends on:* …

## Data and storage | Données et stockage
<!-- nvb:data mandatory -->
<!-- guide: Entities, schema changes, migration (forward, and backward or why not), retention, volume. Write "No change, because <reason>." if nothing changes. A schema change without a migration strategy is an open question. -->

## Interfaces | Interfaces
<!-- nvb:interfaces mandatory -->
<!-- guide: API, contracts, file and message formats: request, response, errors, versioning, compatibility with existing consumers. Write "No change, because <reason>." if nothing changes. -->

## Security | Sécurité
<!-- nvb:security mandatory -->
<!-- guide: R4. Every subsection is required: write the content, or "Not applicable, because <reason>.", which is refused for a subsection concerned by a TQ-S question answered yes. Every SEC-xx of the PRD must be realised by at least one DES-xx here or elsewhere. -->

### Authentication and authorisation | Authentification et autorisations
### Trust boundaries | Frontières de confiance
### Secrets | Secrets
### Input validation | Validation des entrées
### Sensitive data | Données sensibles

## Errors and edge cases | Erreurs et cas limites
<!-- nvb:errors mandatory -->
<!-- guide: For each DES: the failure modes, the expected behaviour, and what the user or caller sees. Include partial failure, retries and concurrency when they apply. -->

## Verification strategy | Stratégie de vérification
<!-- nvb:verification mandatory -->
<!-- guide: One row per REQ and SEC (NFR when measurable). The type of check and what it observes. A check that only reads the code is not a verification. -->

| ID | Check type | What is observed |
|---|---|---|
| REQ-01 | {{unit / integration / e2e / command / measurement}} | {{…}} |

## Decisions | Décisions
<!-- nvb:decisions mandatory -->
<!-- guide: ADRs first (in force only, R2), then the design decisions that failed the ADR test, each with its reason in one line. For each question in the triage's adr_imposed_by, cite the ADR that covers it. For a question with a derogation of kind adr, write "ADR derogated, see triage.md". -->

| Decision | Where | Imposed by |
|---|---|---|
| {{…}} | ADR-{{xxxx}} | {{TQ-S2}} |
| {{…}} | this design (failed ADR test: {{condition}}) | — |

## Operations | Exploitation
<!-- nvb:operations mandatory -->
<!-- guide: Limited to what this design imposes on the Deploy and Monitor phases: configuration, secrets to provision, migration order, feature flags, rollback, signals to monitor and alerts for the failures listed above. Each item becomes an OPS-xx. Write "Not applicable, because <reason>." if there is none. -->

- **OPS-01** — {{constraint}}.

## Concurrent changes | Changements concurrents
<!-- nvb:concurrent mandatory -->
<!-- guide: Entries of system/pending.md from OTHER initiatives that touch the same system sections, and the conflict, if any. Write "None." if there is none. A conflict that is not settled becomes an open question. -->

## Coverage | Couverture
<!-- nvb:coverage mandatory -->
<!-- guide: R6. One row per REQ, SEC and NFR of the PRD. Then the RES and PRO this design relies on (only "verified" RES). -->

| Upstream ID | Covered by | How |
|---|---|---|
| REQ-01 | DES-01 | {{…}} |

## Open questions | Questions ouvertes
<!-- nvb:open-questions mandatory -->
None.

## Change log | Historique
<!-- nvb:changelog mandatory -->

| Version | Date | Added | Modified | Removed | Note |
|---|---|---|---|---|---|
| 1 | {{YYYY-MM-DD}} | {{DES-01…, OPS-01…}} | — | — | Initial version |
