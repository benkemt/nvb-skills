---
name: nvb-triage
description: Entry point and dashboard of the nvb ("no vibe coding") workflow for the Requirements, Design and Plan phases. Classifies a change (bug fix, small feature, complex feature, new product) with closed security, irreversibility and unknown questions, assigns the initiative number N-xxx, and writes docs/nvb/N-xxx-slug/triage.md. On every later call it shows the state of each required deliverable (absent, draft, open questions, approved, stale) and the single next action. It also handles reclassification. Never runs the other nvb skills. Use when starting any new change, feature, bug fix or product the nvb way, when asked where an initiative stands or what comes next, for an nvb status or dashboard, or when a deliverable raised a reclassification question.
argument-hint: "[N-xxx | description of the change]"
---

# nvb-triage

Read these files before doing anything else. They are part of this skill:

- `${CLAUDE_PLUGIN_ROOT}/references/rules.md`: R1 to R9
- `${CLAUDE_PLUGIN_ROOT}/references/procedures.md`
- `${CLAUDE_PLUGIN_ROOT}/references/triage-questions.md`
- `${CLAUDE_PLUGIN_ROOT}/references/templates/triage.md`

This skill **never runs** another nvb skill. It computes and displays the next one.

## Input contract

| Argument | Mode |
|---|---|
| free text describing a change | **New initiative** |
| `N-xxx` (or a folder name) whose `triage.md` is a draft with open questions or unanswered questions | **Continue**: resume the "New initiative" interview where it stopped, only on what is open |
| `N-xxx` with a pending reclassification question (R9) in one of its deliverables, a task in `state: reclassify`, or the person asks to change an answer or the level | **Reclassification** |
| `N-xxx`, and the person asks to derogate (a level below the floor, or no ADR for an imposed question) | **Derogation** |
| `N-xxx` otherwise | **Dashboard** of that initiative |
| empty | **Dashboard** of all initiatives. If `docs/nvb/` does not exist, ask what the change is and switch to **New initiative** |

When several of these modes apply, run them in this order: Continue, then Reclassification, then
Derogation. End every mode that writes with the Dashboard of the initiative.

Preconditions:

- Work at the root of the target repository. If it is not a git repository, warn that the initiative
  number cannot be checked against other branches, and continue only if the person agrees.
- Read the configuration (procedure "Read the configuration").

## Mode: New initiative

1. **Understand the request.** Restate it in 3 to 6 lines and ask the person to confirm it. This text
   becomes the "Request" section.
2. **Gather evidence before asking.** Search the repository for what the change will touch: the
   modules named, authentication and authorisation code, schemas and migrations, public API and
   contract files, configuration that holds secrets. For every fact, note what it says in plain words
   and its full reference (file path and line).
3. **Ask the ten questions** of `triage-questions.md` in order (S, I, then U), **one at a time** (R3).
   Each question has three parts, and must be understandable without opening any file (R3 "Explain,
   do not point"):
   - the triage question itself, in plain words (for example "Does the change touch authentication,
     authorisation or permissions?"), not just its ID;
   - the facts found, **explained** in one or two sentences each, with the full file path and line in
     parentheses after the explanation;
   - your recommended answer and its reason. Then wait.

   Challenge the answer when:
   - the person says "no" to `TQ-S1` but the code touched handles emails, names, identifiers,
     addresses, health or payment data, or logs them;
   - the person says "no" to `TQ-I2` but a DTO, a public route, a file format or a message schema
     changes;
   - the person says "no" to `TQ-U1` but a package or SDK has to be added;
   - the request sounds small, but the evidence spans several components (then ask `TQ-U3` again).
4. **Level.** Compute the floor. Propose a level at or above it, with one line of reason, and ask for
   confirmation.
   - If the person picks a level below the floor, this is a derogation of kind `level`. Apply the
     steps of the Derogation mode (justification written by the person, verbatim).
   - For a level choice between `complex-feature` and `new-product`, remind them that the only
     difference is the adversarial review of the PRD, and recommend `new-product` when `TQ-U3` is yes.
5. **Required deliverables.** Fill them in from the level and the `TQ-U` answers. Set
   `adr_imposed_by` from the `TQ-S`/`TQ-I` answers that are yes, minus the derogations of kind `adr`.
6. **Number and slug.** Apply the procedure "Next initiative number". Propose a slug of at most five
   kebab-case words, in English whatever the language, and ask for confirmation.
7. **Write** `docs/nvb/N-xxx-<slug>/triage.md` from the template: version 1, `status: draft`. Keep
   the front matter and the "Answers" table consistent (same yes/no).
8. **Hand over** (procedure "Hand over to the person"). The next step is for the person to approve
   the triage. Every other skill refuses to start until then.

## Mode: Dashboard

For one initiative:

1. Read `triage.md`, then every required deliverable, the ADRs cited or imposed, `tasks/*.md`, and
   `docs/nvb/system/system.md` and `pending.md`.
2. Compute each state (procedure "Compute the state of a deliverable"). Look for open questions with
   an R3 prefix in **every** deliverable of the initiative. A `Reclassification:` question is pending
   until a triage change log row names it (R9).
3. Print, without writing any file:

```
N-007-auth-tokens — complex-feature (floor complex-feature) · triage@2 approved
Derogations: none
ADR imposed by: TQ-S2 → ADR-0004 proposed

Deliverable  State                Covers                Note
triage       approved             —
prd          approved             triage@2
research     stale                triage@2, prd@1       prd@2 changed REQ-03
prototype    not-required
design       absent
plan         —                    (waits for design)

Next: /nvb:nvb-research N-007 — delta review of REQ-03
```

4. Pick the **single next action**, using the first rule that applies:
   1. **Pending reclassification**: a `Reclassification:` question that no triage change log row
      names yet, or a task in `state: reclassify` → `/nvb:nvb-triage N-xxx` (Reclassification).
   2. **Triage**: not approved → if it has open questions, `/nvb:nvb-triage N-xxx` (Continue).
      Otherwise the person approves `triage.md` (print the lines). If it is stale or
      approved-invalid, say so.
   3. **System**: the initiative requires a design, and `system/system.md` is absent while the
      repository has code, or it is a draft, or the design touches a subsection marked
      `Not documented.` → `/nvb:nvb-design N-xxx bootstrap`. If the system is a draft with no open
      question, the person approves `system.md` instead.
   4. **Chain**: take the first required deliverable, in chain order (prd, research, prototype,
      design, then plan or direct task), that is not `approved`:
      - `absent` → its skill. When that skill is `nvb-plan`, also print "ready for Plan";
      - `stale` → its skill (delta review), naming the changed IDs;
      - `open-questions` → for each question with an R3 prefix, the skill that settles it
        (`ADR required:` → `/nvb:nvb-adr N-xxx new`; `ADR supersession required: ADR-xxxx` →
        `/nvb:nvb-adr N-xxx supersede ADR-xxxx`; `PRD change required:` → `/nvb:nvb-prd N-xxx`;
        `Design change required:` → `/nvb:nvb-design N-xxx`). For the other questions, the
        deliverable's own skill. A settled reclassification question also goes to the
        deliverable's own skill, which closes it;
      - `draft` with no open question → the person approves it (print the lines);
      - `approved-invalid` → the person, with the failing condition and the way out (R2).
   5. **ADRs**: the design cites an ADR that is not in force, or an imposed question has no ADR and
      no derogation → `/nvb:nvb-adr N-xxx`, or the person accepts the ADR (print the lines).
   6. **Task files**: the plan is approved, but the task files are absent or derived from an older
      plan version → `/nvb:nvb-plan N-xxx`.
   7. **Tasks**: some tasks are neither `done` nor `removed` → list them with their state. The next
      tasks are the `todo` tasks whose `depends_on` are all `done`. A `verified` task waits for
      review by the person, who sets `state: done`.
   8. **Report**: every task is `done` or `removed`, and this initiative has a row in `pending.md`:
      - with no "Reported in" → `/nvb:nvb-design N-xxx report`;
      - "Reported in" `system@M` and `system.md` still a draft → the person approves `system@M`
        (or `/nvb:nvb-design N-xxx` if it has open questions).
   9. **Closed**: every task is `done` or `removed`, and the initiative has no row in `pending.md`
      (R7). Say so. A closed initiative is never stale.
5. **Parallel waves** (display only, never written): when tasks exist, group the tasks that are
   neither `done` nor `removed` into waves. A task belongs to the first wave in which all its
   `depends_on` are done or in earlier waves, **and** its `scope` overlaps with no other task of the
   same wave. When two scopes overlap, put the later task (by ID) in the next wave and say why.
6. Always show the derogations, with their kind, on every dashboard (`triage-questions.md`).

For all initiatives: print one line per initiative, with its number, level, the state of its least
advanced deliverable, and its next action. Then list the rows of `pending.md`.

## Mode: Reclassification

1. Quote what triggers it:
   - a pending `Reclassification:` question: the question, its file, its evidence;
   - a task in `state: reclassify`: the task, the files changed outside its `scope` (from its
     execution log, or `git diff --stat`);
   - a request from the person: quote it.
2. Ask each triage question concerned again, one at a time, with the evidence (R3). For a task in
   `reclassify`, first ask whether the overflow reveals a fact that answers "yes" to a question
   answered "no".
3. **If the triage must record something** (an answer changes, or a `Reclassification:` question
   is answered, even with "no"): if `triage.md` is approved, apply the procedure "Start a new
   version". If it is a draft, edit it in place. The change log row names each question settled:
   `TQ-xx re-examined (Q-yy of <file>): yes|no, because …`. That row is what makes the question
   settled (R9).
   **If nothing changes** (a task in `reclassify` whose overflow answers no triage question), do not
   touch the triage. Send the person to `/nvb:nvb-plan N-xxx` for a new plan version (scope or
   split). `nvb-plan` will then take the task out of `reclassify`.
4. Recompute the floor. If it is now above the current level, propose a new level or a derogation
   (Derogation mode). Update `required` and `adr_imposed_by`.
5. Do **not** edit the other deliverables. Tell the person:
   - the deliverables that become stale (all those that cite `triage@<previous>`), and which skill
     reviews each one. The skill that raised a settled question closes it during that review;
   - the deliverables that became required, and the ones that are no longer required (their files
     stay; they are simply ignored).
6. Hand over: the person approves the new version of the triage.

## Mode: Derogation

The person asks to go below what the answers impose (`triage-questions.md` "Derogation"):

- kind `level`: a level below the floor;
- kind `adr`: no ADR for a `TQ-S`/`TQ-I` question that imposes one. This is typically proposed by
  `nvb-adr`, when an imposed decision fails the eligibility test.

1. Restate what the derogation removes: deliverables no longer produced, or an ADR not written. Then
   ask the person to confirm it, one question at a time.
2. Ask the person to **write the justification themselves**. Record it **verbatim**, with their name
   and the date. Never write the justification for them, and never rephrase it.
3. Record it in the front matter (`derogations`) and in the "Derogations" section. For kind `adr`,
   remove the question from `adr_imposed_by`. For kind `level`, update `level` and `required`.
4. If `triage.md` is approved, apply "Start a new version". If it is a draft, edit it in place.
5. Hand over: the person approves the triage. Say that the derogation stays visible on every
   dashboard and in every task.

## Never

- Write `approved`, or fill `approved_by` / `approved_on` (R2).
- Run or simulate another nvb skill: no PRD question, no design, no plan here.
- Reuse an initiative number, or renumber an initiative when its slug changes.
- Change an answer without a new version once the triage is approved.
- Write a derogation's justification for the person.
- Write anything in Dashboard mode.
