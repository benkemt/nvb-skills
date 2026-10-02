---
name: nvb-research
description: Produces research.md for an nvb initiative. It settles the unknowns flagged by the triage (API, library or technology never used) with verified sources, and each RES-xx finding records the URL or path, the version concerned, the consultation date and an exact quote copied from the source. Findings without a quote are marked unverified and cannot be relied on by the design. Questions that need executable proof are handed to the prototype. Use when asked to research, investigate or document an external API, library, SDK, protocol or technology for an nvb initiative, to write or update research.md, or when nvb-triage names nvb-research as the next action.
argument-hint: "[N-xxx]"
---

# nvb-research

Read before anything else:

- `${CLAUDE_PLUGIN_ROOT}/references/rules.md`
- `${CLAUDE_PLUGIN_ROOT}/references/procedures.md`
- `${CLAUDE_PLUGIN_ROOT}/references/templates/research.md`

## Input contract

- **Argument**: the initiative (`N-xxx`), or nothing (procedure "Locate the initiative").
- **Required, approved and fresh**: `triage.md` with `research` in `required` (from `TQ-U1`), plus
  `prd.md` if the triage requires a PRD. If research is not required, stop. Research is triggered
  only by `TQ-U1`, never by the level.
- **Read**: the `TQ-U1` evidence of the triage, and the PRD IDs that depend on the unknowns. In the
  repository, read the dependency manifests (`*.csproj`, `package.json`, lock files,
  `requirements*.txt`, `go.mod`…) to learn which version, if any, is already in use.

Apply the procedure "Check the upstream gate". If it fails, stop.

Modes: as in every nvb skill. **Create** when absent, **Continue** when draft, **Delta review** when
stale, **New version** when approved and the person asks for a change.

## Steps

1. **Agree on the scope** (R3, one question at a time). Propose the list of unknowns to settle, each
   one linked to the PRD IDs that need it. The person confirms, removes or adds items. Nothing is
   searched outside this list. A new unknown discovered later is proposed to the person first.
2. **Search** each unknown, primary sources first: official documentation for the **version** that
   will be used, the specification, the source code of the library, release notes and changelogs. A
   blog post or forum answer can only point to a primary source. It is never cited as the source.
3. **Record each finding** as one `RES-xx`, with all six fields of the template. The quote is
   **copied** from the content actually fetched or read, never written from memory.
4. **Verify each quote** before writing: fetch or read the source again, and check that the quoted
   text is there (ignore whitespace differences only). If it is not there, or the source cannot be
   reached, mark the finding `unverified`, and say why in the conclusion.
5. **Check the version**: if the documented version differs from the one in the repository's
   manifests, or from the one the PRD imposes, record it as a finding of its own, or as an open
   question.
6. **Present the findings** to the person, one at a time when one of them changes something:
   options ruled out by the facts, surprising limits, security consequences. The person may challenge a finding.
   The finding then gets another source, or becomes `unverified`, or becomes an open question.
7. **Questions for the prototype**: any question that only running code can settle (performance,
   behaviour under real conditions, a combination nobody documents) goes into "Questions for the
   prototype". If the triage does not require a prototype, also raise a reclassification question
   for `TQ-U2` (R9).

### Challenge points

- A source that describes another version than the one that will be used.
- A conclusion that goes beyond what the quote says. The quote must support the conclusion on its own.
- A single secondary source.
- Documentation that contradicts the observed behaviour of the code in the repository. Quote both
  sides.
- A security limit found in the sources (deprecated algorithm, default insecure setting, known CVE).
  Record it as a `RES`, and ask whether it changes a `SEC` of the PRD. If it adds a security subject
  absent from the triage, raise a reclassification question (R9).

## Write

- Use `covers: [triage@<n>, prd@<n>]`, with only the deliverables that exist. Number the IDs
  sequentially, and never reuse one (R5).
- Write in the repository language (R8). The quotes stay in the source's language.
- Every unsettled point becomes a `Q-xx`.

## Hand over

Apply the procedure "Hand over to the person". The next skill is `nvb-prototype` if required,
otherwise `nvb-design`.

## Never

- Write `approved` (R2), or modify the PRD or the triage (R6).
- Write a quote from memory, paraphrase a quote, or put quotation marks around a summary.
- Choose between options: that is a design decision, and possibly an ADR. Research reports the facts
  that will inform the choice.
