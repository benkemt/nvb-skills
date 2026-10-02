---
nvb: prototype
initiative: {{N-xxx-slug}}
version: 1
status: draft
approved_by: ""
approved_on: ""
covers: [triage@{{n}}, prd@{{n}}, research@{{n}}]   # only those the triage requires
branch: nvb/proto/{{N-xxx-slug}}                      # never merged
commit: "{{full commit hash of the evidence}}"
---

# {{N-xxx}} — {{title}} · Prototype

## Question
<!-- nvb:question mandatory -->
<!-- guide: Written and confirmed by the person BEFORE any code. One question per item, each one answerable by yes, no or a measurement. Also state what result would answer each way. -->

- {{question}} — *answers yes if:* {{observable result}}; *answers no if:* {{observable result}}.

## Answer | Réponse
<!-- nvb:answer mandatory -->
<!-- guide: The direct answer to each question, one line each, in the same order. -->

## Findings | Constats
<!-- nvb:findings mandatory -->
<!-- guide: One PRO per lesson learned. Each one points to the command and output below that proves it. Report what the run showed, not what the code was meant to do. -->

- **PRO-01** — {{lesson, one sentence}}. *Evidence:* run {{1}}.

## Evidence | Preuves
<!-- nvb:evidence mandatory -->
<!-- guide: Enough for the branch to be deleted without losing the proof. Branch, commit hash, environment (OS, runtime and library versions), then for each run: the exact command and its output, trimmed to the relevant lines with "[…]" marking each cut. -->

- *Branch:* `nvb/proto/{{N-xxx-slug}}` · *Commit:* `{{hash}}`
- *Environment:* {{OS, runtime, library versions}}

### Run 1

```console
$ {{command}}
{{output}}
```

## What must not be carried over | Ce qui ne doit pas être repris
<!-- nvb:not-carried mandatory -->
<!-- guide: The prototype code is never reused as-is. List the shortcuts taken (no error handling, hard-coded secrets, no tests, no validation…), so that the design does not inherit them. -->

## Open questions | Questions ouvertes
<!-- nvb:open-questions mandatory -->
None.

## Change log | Historique
<!-- nvb:changelog mandatory -->

| Version | Date | Added | Modified | Removed | Note |
|---|---|---|---|---|---|
| 1 | {{YYYY-MM-DD}} | {{PRO-01…}} | — | — | Initial version |
