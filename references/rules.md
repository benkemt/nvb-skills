# nvb — common rules (R1–R9)

Every `nvb-*` skill applies these rules. They are written here once and never copied into a skill.
A skill that needs a rule cites it by number (`R3`) and reads this file.

Scope of the `nvb` family: the **Requirements**, **Design** and **Plan** phases.
Build, Review, Deploy and Monitor are out of scope. The only contact points with them are listed in
`templates/task.md` (Build contract) and in the "Operations" section of `templates/design.md`.

Skills never invoke one another. Each one does its own job, and then says which skill comes next.
`nvb-triage` is the dashboard that computes and displays that next step.

---

## R1 — Location

All deliverables are Markdown files in the target repository, under `docs/nvb/`. That folder is the
single source of truth, and the Build agent reads it. There is no tracker in v1.

```
docs/nvb/
  config.md                         # language (R8)
  system/                           # system@N
    system.md                       # index: version, approval, section map
    architecture.md
    data-model.md
    security-model.md
    pending.md                      # approved deltas not yet reported
    decisions/ADR-0001-<slug>.md
  N-007-<slug>/                     # one folder per initiative
    triage.md
    prd.md
    research.md
    prototype.md
    design.md                       # delta against system@N
    plan.md
    tasks/T-01-<slug>.md
```

A file that the triage does not require is never created. It does not even get an empty placeholder.

---

## R2 — Approval

### Front matter

Every deliverable starts with a strict YAML front matter. Keys are always in English, whatever the
language of the content (R8). The exact schema of each kind is in its template under `templates/`.
These keys are common to the initiative deliverables (triage, prd, research, prototype, design, plan,
direct task). `system/system.md` has them too, except `initiative` and `covers`.

```yaml
nvb: prd                 # kind of deliverable
initiative: N-007-auth-tokens
version: 1               # integer, +1 on each new version (R7)
status: draft            # draft | approved
approved_by: ""          # written by the person only
approved_on: ""          # YYYY-MM-DD, written by the person only
covers: []               # upstream fingerprints (R7), e.g. [triage@1]
```

### Who writes what

Only the person writes these values and fields:

| Deliverable | Value | With |
|---|---|---|
| initiative deliverables, direct task, `system.md` | `status: approved` | `approved_by`, `approved_on` |
| ADR | `status: accepted` or `status: revised` | `decided_by`, `decided_on`, a History row |
| task (execution) | `state: done` | — |

- A skill never writes one of these values into a field. Template comments that explain who writes
  them are allowed. If the person asks the skill to "approve it for me", the skill refuses and shows
  the exact lines the person has to edit (procedure "Hand over to the person").
- A skill writes `draft`, increments `version`, and fills `covers`. For an ADR it writes `proposed`,
  `revision-proposed` and `superseded`. For a task it writes `todo`, `in-progress`, `verified`,
  `reclassify` and `removed`.
- An ADR is **in force** when its status is `accepted`, `revised` or `revision-proposed`. In that last
  case the decision is unchanged and a correction is waiting for the person. "Accepted ADR" means an
  ADR in force everywhere in these files.

### Gate check

Each skill refuses to start until every deliverable upstream of it is **approved and fresh**. The
upstream deliverables are the ones that the approved triage requires and that come earlier in the chain:

```
triage → prd → research → prototype → design (+ ADRs) → plan → tasks
```

ADRs sit outside the chain. Their own gate is the triage only (`procedures.md`), and the design and the
plan require the ADRs they cite to be in force.

A deliverable is **approved and fresh** only when all of these conditions hold:

1. `status: approved`, with `approved_by` not empty and `approved_on` a valid date. For an ADR, the ADR
   is in force, with `decided_by` and `decided_on` filled;
2. its "Open questions" section contains no open question (R3);
3. none of its mandatory sections is empty (R4 for security);
4. its coverage table has no uncovered row (R6), if it has one;
5. it is not stale (R7).

If `status: approved` but conditions 2 to 4 fail, the deliverable is **approved-invalid**. The skill
refuses to build on it and reports which condition failed. Someone wrote `approved` on a deliverable
that does not meet the rules, and only the person can decide what to do about it. The way out is for
the person to set `status: draft` and empty `approved_by` and `approved_on`. The deliverable's skill
then continues it like any draft.

When the gate fails, the skill stops before asking anything. It prints the failing deliverable, its
state, and the skill that fixes it.

---

## R3 — Interview first

Every deliverable is produced through an interview with the person. Each skill also has to challenge
assumptions: there is no separate skill for that.

- Ask **one question at a time**. Each question comes with a **recommendation** and the reason for it,
  based on what the repository, the upstream deliverables or the sources show. Then wait for the answer.
- Before asking, read what can be read: the upstream deliverables, the code, the existing `system/`.
  Do not ask the person for what the repository already says. Explain it instead, and ask them to
  confirm it.
- **Explain, do not point.** The person has not read the files you read. Each question must make
  sense on its own:
  - State each fact **in plain words**: what the code or the document does or says, and why it
    matters for the question. Quote a short extract (one or two lines) when the exact wording
    matters.
  - A reference is only a pointer that comes **after** the explanation, and it always gives the full
    file path: `(docs/reference.md, line 203)`. Never write a bare reference such as `:203`, `l.164`
    or "see line 505", and never use a reference in place of the explanation.
  - Explain any identifier, acronym or technical name the person may not know in a few words, the
    first time it appears. For example: "`DefaultAzureCredential`, the Azure SDK class that tries
    several authentication methods in turn".

  Example. Wrong: "TQ-S2: DefaultAzureCredential and method choice in the profile (:203, :205),
  prohibitions imposed by the tool (:164, :505). Your answer?"
  Right: "TQ-S2, authentication and authorisation. The reference document says that the tool
  authenticates to Azure with `DefaultAzureCredential` (the Azure SDK class that tries managed
  identity, environment variables and then the Azure CLI login, in turn), and that the user chooses
  the method in their profile (docs/reference.md, lines 203–205). It also forbids storing a password
  or a key in the profile (docs/reference.md, lines 164 and 505). The change therefore touches
  authentication, so I recommend **yes**. Do you confirm?"
- The same rule applies to what is **written** in the deliverables: evidence is a sentence that
  states the fact, followed by its reference (full path and line), never a reference alone.
- **Challenge**: when an answer rests on an unstated assumption, contradicts an upstream deliverable or
  the code, or leaves a case unhandled, say so in the next question. Each skill lists its own challenge
  points.
- The deliverable contains **only what was said or confirmed by the person**. A proposal from the agent
  that the person did not confirm is not written as a fact.
- Anything not settled becomes an **open question** in the "Open questions" section:

  ```markdown
  - **Q-03** — <question>. *Blocks:* <IDs or sections concerned>. *Raised by:* <skill>, <YYYY-MM-DD>.
  ```

  An open question blocks approval (R2, condition 2). When it is answered, the answer goes into the
  relevant section, and the question is removed from the list. Git history keeps the trace.
- Some open questions can only be settled by **another** skill. Their text starts with a fixed prefix,
  so that `nvb-triage` sends the person to the right skill. The prefix is in English whatever the
  language (R8):

  | Prefix | Settled by |
  |---|---|
  | `Reclassification:` | `nvb-triage` (R9) |
  | `ADR required:` | `nvb-adr new`, then the person accepts the ADR |
  | `ADR supersession required: ADR-xxxx` | `nvb-adr supersede ADR-xxxx` |
  | `PRD change required:` | `nvb-prd` (new version) |
  | `Design change required:` | `nvb-design` (new version) |

  The skill that raised the question removes it on its next run, once the other skill has settled
  it.
  If there are no open questions, the section contains exactly `None.` (or `Aucune.` in French).
- Stop the interview when every mandatory section has content or is covered by an open question. Do
  not keep the person in an endless interview: an open question is a legitimate outcome.

---

## R4 — Security is mandatory

- The PRD has a "Security requirements" section (`SEC-xx`) and the design has a "Security" section.
  Neither can be empty, and an empty section blocks approval.
- When the subject does not apply, the section says so explicitly:
  `Not applicable, because <reason>.` (French: `Sans objet, parce que <raison>.`).
  The section is never removed.
- When the triage answered "yes" to a security question (`TQ-S*`), "not applicable" is refused. The
  section must contain at least one `SEC-xx`.

---

## R5 — Identifiers

Identifiers are stable. An identifier is never renumbered or reused, even after it is removed. A
removed ID is listed in the change log (R7) with the word `removed`.

| ID | Where defined | Pattern |
|---|---|---|
| Initiative | `triage.md` | `N-\d{3,}-[a-z0-9]+(-[a-z0-9]+)*` (e.g. `N-007-auth-tokens`) |
| Requirement | `prd.md` | `REQ-\d{2,}` |
| Security requirement | `prd.md` | `SEC-\d{2,}` |
| Non-functional requirement | `prd.md` | `NFR-\d{2,}` |
| Research finding | `research.md` | `RES-\d{2,}` |
| Prototype finding | `prototype.md` | `PRO-\d{2,}` |
| Design element | `design.md` | `DES-\d{2,}` |
| Operations constraint | `design.md` | `OPS-\d{2,}` |
| Task | `plan.md`, `tasks/` | `T-\d{2,}` |
| Open question | any deliverable | `Q-\d{2,}` (local to the file) |
| Triage question | `triage-questions.md` | `TQ-[SIU]\d` |
| Decision | `system/decisions/` | `ADR-\d{4}` (global to the repository) |

- **Initiative number**: `N-<number>-<slug>`. The number is stable and is never reused. The slug is
  cosmetic and may change. The folder name follows the slug, but every reference uses the number.
  `nvb-triage` assigns the number (see `procedures.md`, "Next initiative number").
- **References inside an initiative**: plain (`REQ-03`).
  **References across initiatives**: qualified (`N-007/REQ-03`).
- An ID is defined exactly once, at the start of a list item or a table row: `- **REQ-03** — …` or
  `| REQ-03 | … |`. Everywhere else it is cited.

---

## R6 — Traceability

- The coverage table lives in the **downstream** deliverable. An approved upstream deliverable is never
  modified by a downstream skill. The downstream skill reads it.
- Coverage table format (one row per upstream ID, none skipped):

  ```markdown
  | Upstream ID | Covered by | How |
  |---|---|---|
  | REQ-01 | DES-02, DES-04 | <one line> |
  | SEC-01 | DES-05 | <one line> |
  | NFR-01 | — | **Not covered** → Q-02 |
  ```

- An uncovered upstream ID blocks the approval of the downstream deliverable (R2, condition 4). It
  shows `—` and points to an open question.
- `plan.md` uses a variant of this table with two columns, "Realised by" and "Verified by",
  because a requirement has to be both built and checked (see `templates/plan.md`).
- What each deliverable covers:

  | Downstream | Must cover |
  |---|---|
  | `design.md` | every `REQ`, `SEC`, `NFR` of the PRD, plus every `RES`/`PRO` it relies on (cited, not covered) |
  | `plan.md` | every `REQ`, `SEC` (realised **and** verified), every `DES`, every `OPS` (taken by a task or handed over explicitly) |
  | direct task | its embedded `REQ`/`SEC` (each verified by a success criterion), plus any `RES`/`PRO` it relies on |

---

## R7 — Change

- Every deliverable has an integer `version`. To change an **approved** deliverable, its own skill
  starts a new version: `version` +1, `status: draft`, `approved_by` and `approved_on` emptied, and
  a new change log entry.
- Every downstream deliverable records the versions it was checked against in `covers`:
  `covers: [triage@1, prd@2, research@1, system@4]`. ADRs are cited by ID (`ADR-0003`), because an
  accepted ADR does not change in substance.
- A deliverable is **stale** when at least one of these is true, **whatever its own status** (a draft
  can be stale too):
  - it cites `<kind>@n` while that deliverable is now at a version greater than `n`, whatever its status;
  - it cites an ADR whose status is `superseded`;
  - it cites `system@n` and one of the system sections it lists in `system_sections` changed in a
    version greater than `n` (see `templates/system.md`). A system version whose change log row
    names **the same initiative** is ignored, because that is the report of its own delta.
- A **closed** initiative is never stale. An initiative is closed when every task is `done` or
  `removed`, and its design has no row left in `system/pending.md` (or it has no design). Its
  deliverables are then frozen as the record of what was decided.
- Every deliverable ends with a change log:

  ```markdown
  | Version | Date | Added | Modified | Removed | Note |
  |---|---|---|---|---|---|
  | 2 | 2026-10-14 | REQ-07 | REQ-03, SEC-01 | REQ-05 | <one line> |
  ```

- **Delta review**: when a deliverable is stale, its skill reviews **only** the IDs that the upstream
  change log lists as added, modified or removed between the cited version and the current one. Then
  it updates the coverage table and `covers`. An approved deliverable gets a new version, which goes
  back to `draft`. A draft is edited in place.

---

## R8 — Language

- One language per repository, recorded in `docs/nvb/config.md` (`language: en | fr`). English is the
  default. The first skill that finds no `config.md` asks the question once, recommends `en`, and
  writes the file.
- Front matter keys, IDs, statuses, file names and section anchors (`<!-- nvb:… -->`) are always in
  English. Headings and content follow the language of the repository. Each template gives the
  heading in both languages.
- If the interview is held in another language than the repository language, each requirement,
  finding or decision is **read back to the person in the interview language** before it is written
  in the repository language. The written version must say the same thing, no more and no less.

---

## R9 — Reclassification

- When a skill discovers a fact that answers "yes" to a triage question that was answered "no"
  (`triage-questions.md`), it records an open question in its own deliverable:

  ```markdown
  - **Q-04** — Reclassification: <fact explained in plain words (full/path.ext, line N)> answers "yes" to TQ-S2. *Blocks:* this deliverable. *Raised by:* nvb-design, 2026-10-14.
  ```

- This question blocks that deliverable. Only `nvb-triage` settles it, with a new version of
  `triage.md` (or an in-place edit if the triage is still a draft). The triage change log row then
  names the question: `TQ-S2 re-examined (Q-04 of design.md): yes|no, because …`.
- A reclassification question is **pending** as long as no triage change log row names it. A pending
  question blocks the gate of every skill of the initiative (`procedures.md`). Once a row names it,
  the question is **settled**. It no longer blocks the other skills, and the skill that raised it
  removes it on its next run. That run is usually a delta review, because the deliverable now cites
  an older triage.
- The skill tells the person to run `nvb-triage` and then come back.
- A task in `state: reclassify` (its diff left its announced scope during the Build) is also a
  trigger for `nvb-triage`. If no triage answer changes, the triage stays as it is, and the person is
  sent to `nvb-plan` for a new plan version (scope or split).
