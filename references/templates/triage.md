---
nvb: triage
initiative: {{N-xxx-slug}}
title: "{{one-line title}}"
version: 1
status: draft
approved_by: ""
approved_on: ""
covers: []
level: {{bug-fix|small-feature|complex-feature|new-product}}
floor: {{bug-fix|complex-feature}}
answers:            # true | false, one key per question of triage-questions.md
  TQ-S1: {{false}}
  TQ-S2: {{false}}
  TQ-S3: {{false}}
  TQ-S4: {{false}}
  TQ-I1: {{false}}
  TQ-I2: {{false}}
  TQ-I3: {{false}}
  TQ-U1: {{false}}
  TQ-U2: {{false}}
  TQ-U3: {{false}}
required: [{{prd, research, prototype, design, plan, direct-task}}]
adr_imposed_by: [{{TQ-S2, TQ-I1}}]   # TQ-S*/TQ-I* answered true, minus derogations of kind adr
derogations:                         # [] if none; kind: level | adr (triage-questions.md "Derogation")
  - {question: {{TQ-S2}}, kind: {{level}}}
---

# {{N-xxx}} — {{title}} · Triage

## Request | Demande
<!-- nvb:request mandatory -->
<!-- guide: What the person asked for, in their words, in 3 to 6 lines. No solution. -->

## Answers | Réponses
<!-- nvb:answers mandatory -->
<!-- guide: One row per question, none skipped. Evidence is required for "yes" and recommended for "no". Evidence = the fact explained in a sentence a reader understands without opening the file, then (full path, line). Never a bare reference (R3). -->

| Question | Answer | Evidence |
|---|---|---|
| TQ-S1 Personal or sensitive data | {{yes/no}} | {{fact explained in plain words (full/path.ext, line N), or the person's statement}} |
| TQ-S2 Authentication / authorisation | | |
| TQ-S3 New exposure | | |
| TQ-S4 Secrets or keys | | |
| TQ-I1 Schema or data migration | | |
| TQ-I2 Published format or contract | | |
| TQ-I3 Deletion of data or feature | | |
| TQ-U1 Never-used API / library / technology | | |
| TQ-U2 Feasibility / interaction uncertainty | | |
| TQ-U3 Several components or teams | | |

## Level | Niveau
<!-- nvb:level mandatory -->
- **Floor:** `{{floor}}`, because {{questions answered yes, or "no TQ-S/TQ-I yes"}}.
- **Proposed by the agent:** `{{level}}`, because {{reason}}.
- **Confirmed by the person:** `{{level}}`.

## Derogations | Dérogations
<!-- nvb:derogations mandatory -->
<!-- guide: Format in triage-questions.md "Derogation". Write "None." / "Aucune." if there is none. -->

## Required deliverables | Livrables requis
<!-- nvb:required mandatory -->

| Deliverable | Required | Why |
|---|---|---|
| prd | {{yes/no}} | {{level}} |
| research | | {{TQ-U1}} |
| prototype | | {{TQ-U2}} |
| design | | {{level}} |
| ADR | | {{imposed by TQ-S2 / ADR test at design time / no}} |
| plan | | {{level}} |
| direct task | | {{bug-fix / small-feature}} |

## Open questions | Questions ouvertes
<!-- nvb:open-questions mandatory -->
None.

## Change log | Historique
<!-- nvb:changelog mandatory -->

| Version | Date | Added | Modified | Removed | Note |
|---|---|---|---|---|---|
| 1 | {{YYYY-MM-DD}} | — | — | — | Initial triage |
