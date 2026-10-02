---
nvb: prd
initiative: {{N-xxx-slug}}
version: 1
status: draft
approved_by: ""
approved_on: ""
covers: [triage@{{n}}]
adversarial_review: {{required|not-required}}   # required when level is new-product
---

# {{N-xxx}} — {{title}} · PRD

## Context and problem | Contexte et problème
<!-- nvb:context mandatory -->
<!-- guide: What exists today, what goes wrong, and why it matters now. Facts said or confirmed by the person only. -->

## Users and actors | Utilisateurs et acteurs
<!-- nvb:actors mandatory -->
<!-- guide: Who uses or is affected: humans, systems, other teams. One line each. No detailed personas. -->

## Requirements | Exigences
<!-- nvb:requirements mandatory -->
<!-- guide: What, not how. Each REQ has a testable acceptance criterion: an observable result that a test or a command can check. A criterion such as "fast", "easy" or "secure" is not testable, so ask for a number or an observable behaviour. No user stories. -->

- **REQ-01** — {{requirement}}.
  *Acceptance:* {{observable, testable criterion}}.

## Security requirements | Exigences de sécurité
<!-- nvb:security mandatory -->
<!-- guide: R4. At least one SEC-xx when the triage answered yes to a TQ-S question. Otherwise SEC-xx items, or "Not applicable, because <reason>." Cover the relevant points: data classification and protection, who may do what, exposure and input validation, secrets, logging of security events, abuse cases. -->

- **SEC-01** — {{security requirement}}.
  *Acceptance:* {{observable, testable criterion, including the abuse case}}.

## Non-functional requirements | Exigences non fonctionnelles
<!-- nvb:nfr -->
<!-- guide: Only the ones the person expressed, with a measurable threshold. Never invent one. Write "None expressed." / "Aucune exprimée." if there is none. -->

- **NFR-01** — {{requirement}}. *Threshold:* {{number + unit + measurement condition}}.

## Constraints | Contraintes
<!-- nvb:constraints mandatory -->
<!-- guide: Imposed technology, deadline, regulation, compatibility, budget. "None." if none was said. -->

## Out of scope | Hors périmètre
<!-- nvb:out-of-scope mandatory -->
<!-- guide: Explicit. What a reader could reasonably expect but that will not be done. -->

## Success criteria | Critères de succès
<!-- nvb:success mandatory -->
<!-- guide: How we will know, after delivery, that the problem is solved. Measurable when possible. -->

## Adversarial review | Revue adverse
<!-- nvb:adversarial-review -->
<!-- guide: Only when adversarial_review: required. List each finding of the independent review with what became of it: an open question, a change accepted by the person (ID), or rejected by the person (reason). -->

## Open questions | Questions ouvertes
<!-- nvb:open-questions mandatory -->
None.

## Change log | Historique
<!-- nvb:changelog mandatory -->

| Version | Date | Added | Modified | Removed | Note |
|---|---|---|---|---|---|
| 1 | {{YYYY-MM-DD}} | {{REQ-01…}} | — | — | Initial version |
