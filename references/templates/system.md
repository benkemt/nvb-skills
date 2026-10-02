# Template — system (system@N)

The system is described by four files under `docs/nvb/system/`. Only the index carries the version
and the approval. The section files carry content only.

---

## `system.md` (index)

```markdown
---
nvb: system
version: {{N}}
status: draft
approved_by: ""
approved_on: ""
origin: {{bootstrap|greenfield}}   # bootstrap = documented from an existing repository
sections:                          # system version in which each section last changed (R7 staleness)
  architecture: {{N}}
  data-model: {{N}}
  security-model: {{N}}
---

# System

<!-- nvb:index mandatory -->
- [Architecture](architecture.md)
- [Data model](data-model.md)
- [Security model](security-model.md)
- [Decisions](decisions/)
- [Pending deltas](pending.md)

## Open questions | Questions ouvertes
<!-- nvb:open-questions mandatory -->
None.

## Change log | Historique
<!-- nvb:changelog mandatory -->

| Version | Date | Sections changed | Initiative | Note |
|---|---|---|---|---|
| 1 | {{YYYY-MM-DD}} | architecture, data-model, security-model | {{N-xxx}} | {{bootstrap / initial}} |
```

---

## `architecture.md`

```markdown
# Architecture

<!-- nvb:system-architecture -->
## Overview | Vue d'ensemble
<!-- guide: One Mermaid diagram of the components and their relationships, then 5 to 10 lines. -->

## Components | Composants
<!-- guide: One subsection per component: responsibility, technology, interfaces exposed and consumed, where in the repository (path). -->

## Interfaces | Interfaces
<!-- guide: Published contracts: APIs, files, messages. Version and consumers. -->

## Deployment and runtime | Déploiement et exécution
<!-- guide: Only what the design phase needs to know: processes, hosts, configuration sources. -->
```

---

## `data-model.md`

```markdown
# Data model

<!-- nvb:system-data-model -->
## Entities | Entités
<!-- guide: One subsection per entity: fields that matter, relationships, owner component, storage. A Mermaid erDiagram when there are more than three entities. -->

## Storage | Stockage
<!-- guide: Databases, files, caches: technology, schema location, migration mechanism. -->

## Sensitive data | Données sensibles
<!-- guide: Classification of each personal or sensitive field, where it is stored and transmitted, retention. -->
```

---

## `security-model.md`

```markdown
# Security model

<!-- nvb:system-security-model -->
## Identities and authentication | Identités et authentification
## Authorisation | Autorisations
## Trust boundaries | Frontières de confiance
## Secrets and keys | Secrets et clés
## Input validation | Validation des entrées
## Security events and logging | Événements de sécurité et journalisation
```

---

## Rules for the section files

- **Bootstrap mode** (`origin: bootstrap`): document only the sections and subsections that the
  initiative touches. Every statement is a full sentence that explains the fact, and ends with its evidence:
  `(src/auth/TokenService.cs, line 42)`.
  Whatever cannot be proven from the repository becomes an open question in `system.md`. A
  subsection nobody has documented yet contains exactly `Not documented.` (`Non documenté.`).
- **Report mode**: the delta of a design is merged into the section files. Each changed subsection
  ends with `(N-xxx, system@<new version>)` to show where it came from.
- A section file never contains `DES-xx` or `REQ-xx` definitions. Those IDs belong to the
  initiatives. It may cite them, qualified (`N-007/DES-02`).
