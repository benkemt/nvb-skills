---
nvb: adr
id: ADR-{{xxxx}}
title: "{{decision as a short sentence}}"
status: proposed       # proposed | accepted | revision-proposed | revised | superseded
                       # the skill writes: proposed, revision-proposed, superseded
                       # only the person writes: accepted, revised (R2)
decided_by: ""         # written by the person, together with accepted
decided_on: ""         # YYYY-MM-DD, written by the person
revision: 0            # +1 for each correction that does not change the decision
initiative: {{N-xxx-slug}}       # initiative where the decision was taken
imposed_by: [{{TQ-S2}}]          # triage questions that impose it, [] if it comes from the ADR test only
decided_against: [triage@{{n}}, prd@{{n}}, research@{{n}}, prototype@{{n}}]   # informational, not subject to R7 staleness (see procedures "Delta review")
supersedes: []         # [ADR-xxxx]
superseded_by: ""      # ADR-xxxx, written by nvb-adr once the successor is accepted
---

# ADR-{{xxxx}} — {{title}}

## Eligibility | Éligibilité
<!-- nvb:eligibility mandatory -->
<!-- guide: All three conditions are required. If one fails, this is not an ADR, and the decision stays in the design. -->

- **Hard or costly to undo:** {{why: data, contract, security, migration cost}}
- **Real alternative considered:** {{name of at least one other option that was actually considered}}
- **Why and why-not written:** see "Decision" and "Considered options".

## Context | Contexte
<!-- nvb:context mandatory -->
<!-- guide: The forces at play, each explained in plain words, then its source in parentheses: REQ/SEC/NFR, RES, PRO, system sections, code (full path, line). Only what was said or what can be proven. -->

## Decision | Décision
<!-- nvb:decision mandatory -->
<!-- guide: One paragraph, in the active voice: "We will…". Then the reason it was chosen. -->

## Considered options | Options envisagées
<!-- nvb:options mandatory -->
<!-- guide: One subsection per option, the chosen one included. Every rejected option has its why-not, stated by the person. -->

### {{Option A}} — chosen
- *Pros:* …
- *Cons:* …

### {{Option B}} — rejected
- *Pros:* …
- *Cons:* …
- *Why not:* {{reason given by the person}}

## Consequences | Conséquences
<!-- nvb:consequences mandatory -->
<!-- guide: What becomes easier, what becomes harder, what is now forbidden (the Build tasks will quote it under "Avoid"), what it costs to reverse later. -->

## Security | Sécurité
<!-- nvb:security mandatory -->
<!-- guide: R4. The security effect of the decision. "Not applicable, because <reason>." is refused when imposed_by contains a TQ-S question. -->

## Open questions | Questions ouvertes
<!-- nvb:open-questions mandatory -->
None.

## History | Historique
<!-- nvb:history mandatory -->
<!-- guide: One row per event: created, accepted (filled in by the person), revised (correction only, with what was corrected), superseded (by which ADR). The previous wording of a revised passage is quoted in the Note, so nothing is lost. -->

| Date | Event | By | Note |
|---|---|---|---|
| {{YYYY-MM-DD}} | created | nvb-adr | {{N-xxx}} |
