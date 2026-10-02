---
nvb: research
initiative: {{N-xxx-slug}}
version: 1
status: draft
approved_by: ""
approved_on: ""
covers: [triage@{{n}}, prd@{{n}}]   # prd only when the triage requires it
---

# {{N-xxx}} — {{title}} · Research

## Scope | Périmètre
<!-- nvb:scope mandatory -->
<!-- guide: The unknowns this research must settle: the TQ-U1 evidence from the triage, plus the PRD IDs that depend on them. One line each, agreed with the person before searching. -->

- {{unknown}} — needed by {{REQ-xx, SEC-xx}}.

## Findings | Constats
<!-- nvb:findings mandatory -->
<!-- guide: One RES per conclusion. Primary sources first: official documentation, specification, source code, changelog. Blog posts and forum answers only to point to a primary source. All six fields are required. Without an exact quote copied from the source, the status is "unverified", and the design may not rely on that finding. -->

- **RES-01** — {{conclusion, one sentence}}.
  - *Question:* {{which unknown it settles}}
  - *Source:* {{URL or repository path}}
  - *Version:* {{version of the API / library / spec the source describes, or "unversioned" + why}}
  - *Consulted:* {{YYYY-MM-DD}}
  - *Quote:* "{{exact text copied from the source}}"
  - *Status:* {{verified|unverified}}

## Questions for the prototype | Questions pour le prototype
<!-- nvb:prototype-questions -->
<!-- guide: Questions that only executable proof can settle. They become the input of nvb-prototype. If the triage did not require a prototype, each one also becomes a reclassification question (R9, TQ-U2). Write "None." if there is none. -->

## Options ruled out by the facts | Options exclues par les faits
<!-- nvb:ruled-out -->
<!-- guide: Only the options that a VERIFIED RES shows to be impossible: incompatible version, missing feature, licence, end of life. Each one cites its RES. Choosing between options that remain possible is a design decision, not research. Write "None." if there is none. -->

## Open questions | Questions ouvertes
<!-- nvb:open-questions mandatory -->
None.

## Change log | Historique
<!-- nvb:changelog mandatory -->

| Version | Date | Added | Modified | Removed | Note |
|---|---|---|---|---|---|
| 1 | {{YYYY-MM-DD}} | {{RES-01…}} | — | — | Initial version |
