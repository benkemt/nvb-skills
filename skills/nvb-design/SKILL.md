---
name: nvb-design
description: Writes the technical design of an nvb initiative as a delta (docs/nvb/N-xxx-slug/design.md) against the versioned system description docs/nvb/system/ (system@N, covering architecture, data model and security model). The delta covers overview with a Mermaid diagram, components, data and migration, interfaces, a mandatory security section, errors, verification strategy, ADR links, operations constraints and a coverage table of every PRD requirement. Three modes. Delta is the interview-driven design. Bootstrap documents, with path:line evidence, only the parts of an existing repository the initiative touches, approved as system@1. Report merges a finished delta into system/ as system@N+1. Use when asked to design, write the technical design, the architecture or the design doc of an nvb initiative, to document the existing system for nvb, to report or merge a delta into the system, or when nvb-triage names nvb-design as the next action.
argument-hint: "[N-xxx] [bootstrap | report]"
---

# nvb-design

Read before anything else:

- `${CLAUDE_PLUGIN_ROOT}/references/rules.md`
- `${CLAUDE_PLUGIN_ROOT}/references/procedures.md`
- `${CLAUDE_PLUGIN_ROOT}/references/triage-questions.md` (imposed ADRs)
- `${CLAUDE_PLUGIN_ROOT}/references/templates/design.md`
- `${CLAUDE_PLUGIN_ROOT}/references/templates/system.md`
- `${CLAUDE_PLUGIN_ROOT}/references/templates/pending.md`

## Input contract

- **Argument**: the initiative (`N-xxx`) and optionally the mode.
- **Mode selection**, when not given:
  1. **system continue** if `system/system.md` is a draft. Resume what its last change log row
     says it came from (bootstrap, extension or report), on its open questions only;
  2. `report` if every task of the initiative is `done` or `removed`, and its row in `pending.md`
     has no "Reported in";
  3. `bootstrap` if `docs/nvb/system/system.md` is absent and the repository already contains code;
  4. `bootstrap` (extension) if the initiative touches a system subsection marked `Not documented.`;
  5. otherwise **delta**.
- **Housekeeping, on every run**: remove from `pending.md` every row whose "Reported in"
  `system@M` is now approved. That initiative is closed (R7).
- **Required for delta**: `triage.md` with `design` in `required`, and `prd`, `research` and
  `prototype` when required, all approved and fresh. `system.md` must be approved, unless the
  repository is greenfield (see below).
- **Required for report**: `design.md` and `plan.md` approved, and every task `done` or `removed`.

Apply the procedure "Check the upstream gate". If it fails, stop. **Greenfield**: when there is no
`system/` and no code yet, there is nothing to bootstrap. The design cites `system@0`, and the
report will create `system@1` with `origin: greenfield`.

---

## Mode: Delta

### Before asking

1. Read the approved upstream deliverables, `system/system.md` and the system sections, every ADR in
   `decisions/` that is in force (R2), and the code the initiative will touch.
2. List the system sections the delta will **read** (`system_sections`) and **change** (`touches`).
3. Read `pending.md`. Any row from **another** initiative that touches the same sections is a
   concurrent change. Read that design, and record the overlap in "Concurrent changes".
4. Build the coverage skeleton, with one row per `REQ`, `SEC` and `NFR` of the PRD.

### Interview (R3)

One question at a time, each with a recommendation and its reason. Follow the template order:
overview, then architecture and components, data and storage, interfaces, security (all five
subsections), errors and edge cases, verification strategy, decisions, operations.
Each answer that defines something a task can implement or verify becomes a `DES-xx`
(or an `OPS-xx` in Operations).

**Decisions.** For every significant choice (storage, protocol, authentication scheme, library,
contract shape), ask the three ADR questions: hard to undo? real alternative? why and why-not?
- If the choice passes all three, it is an ADR. Record an open question
  `ADR required: <decision>`, and tell the person to run `/nvb:nvb-adr N-xxx new <subject>`. The
  design cannot be approved until that ADR is in force and cited. On the next run, replace the open
  question with the citation.
- If it fails, the decision stays in the design, in "Decisions", with the condition that failed.
- For each question in the triage's `adr_imposed_by`, the design must cite an ADR in force. For a
  question with a derogation of kind `adr`, it says "ADR derogated, see triage.md" instead.

### Challenge points

- A `DES` with no `REQ`/`SEC`/`NFR` behind it (gold plating). Ask which requirement it serves, or
  drop it.
- A `REQ`/`SEC` with no `DES` at all.
- A reliance on an `unverified` `RES`, or on a prototype shortcut from "What must not be carried
  over".
- A contradiction with the system description or with an ADR in force. Either follow it, or
  record `ADR supersession required: ADR-xxxx — <reason>` (settled by `nvb-adr`).
- A security subsection marked "Not applicable" although the triage answered yes to a related
  `TQ-S` question.
- A schema change without migration and rollback, or an interface change without a compatibility
  story for existing consumers.
- A verification that only reads code, or a `SEC` verified only on the happy path.
- An operations need (secret, configuration, alert) that nobody owns.
- A fact that answers "yes" to a triage question answered "no": raise a reclassification question
  (R9).

### Write

- `design.md`, from the template: `covers` with the upstream versions and `system@N` (or
  `system@0`), plus `system_sections`, `touches` and `adrs`. Write in the repository language (R8).
- Cite the system, never restate it: `system/architecture.md#components`.
- **`pending.md`**: create it from its template if it is absent. Then add or update this
  initiative's row (design version, base `system@N`, sections touched). Even a draft announces its
  delta.
- Before handing over, check: every PRD ID has a coverage row; the security section has no empty
  subsection; every imposed ADR is cited; every `REQ`/`SEC` has a verification row. Any failure is
  an open question.

---

## Mode: Bootstrap (existing repository)

Goal: describe the **current** system, only where the initiative needs it, so that the delta has a
reference to point to.

1. List the sections and subsections of `templates/system.md` that the initiative will read or
   change. Leave the others as `Not documented.`.
2. For each subsection, read the code and write **only statements that can be proven**, each one
   ending with its evidence `(full/path.ext, line N)`. The statement itself must be understandable
   without opening the file (R3 "Explain, do not point"). Use one Mermaid diagram per section when it helps.
3. Whatever cannot be proven from the repository (intent, undocumented behaviour, runtime
   configuration) becomes an open question in `system.md`, never an assumption. Ask the person
   only the questions that the code cannot answer, one at a time.
4. Write `system.md` (`version: 1`, or N+1 for an extension; `origin: bootstrap`; the `sections`
   map), and the section files. The change log row says `bootstrap` or `extension`, and names the
   initiative.
5. Hand over. The person approves `system.md`. The delta design starts after that, on the next run.

An **extension** (a later initiative needs a subsection still marked `Not documented.`) works the
same way. It creates `system@N+1`, which touches only those subsections.

---

## Mode: Report (delta → system)

1. Check the gate: the design and plan are approved, and every task is `done` or `removed`.
   `system.md` must be approved: two reports never overlap.
2. **Freshness**: if `system.md` changed, since the design's `system@N`, a section that the design
   reads (R7, ignoring this initiative's own rows), then the design is stale. Stop, and run a
   delta review first.
3. For each element of the delta (the `DES` items that change a touched section), find where it is
   implemented (full file path and line). If an element cannot be found in the code, record an open question
   in `system.md`. Never document as built something that is not.
4. Rewrite the touched subsections of the section files to describe the **new** state, not the
   history. Each changed subsection ends with `(N-xxx, system@N+1)`.
5. `system.md`: `version: N+1`, `status: draft`, approval fields emptied. In the `sections` map, set
   each touched section to N+1. Add a change log row.
6. In `pending.md`, set this initiative's "Reported in" to `system@N+1`. Do not remove the row:
   the housekeeping removes it once `system@N+1` is approved. Until then, the initiative is not
   closed.
7. Tell the person which designs and plans of **other** initiatives read the touched sections. They
   become stale (R7), so name them. This initiative's own design and plan do not become stale (R7
   ignores its own report). Then hand over: the person approves `system@N+1`. Every delta design
   waits for that approval, so keep the time between the report and the approval short.

---

## Never

- Write `approved` (R2), or modify the PRD, research, prototype or triage (R6).
- Write an ADR here: that is `nvb-adr`.
- Restate the whole system in a delta, or describe in `system/` something that the code does not
  show.
- Reuse prototype code, or cite an `unverified` research finding as a basis.
