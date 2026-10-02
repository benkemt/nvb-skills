# nvb — common procedures

These are the operational procedures that several skills share. Each one applies the rules in
`rules.md`. A skill cites a procedure by its heading.

---

## Locate the initiative

1. If the argument contains `N-\d{3,}`, the initiative is the folder `docs/nvb/N-<number>-*`. Match on
   the number only, because the slug may have changed.
2. Otherwise, if the current git branch name contains `N-\d{3,}`, use that number.
3. Otherwise, list the folders under `docs/nvb/` that have a `triage.md`. If there is exactly one
   initiative with a deliverable not yet approved, propose it. If there are several, ask which one,
   one question, listing them.
4. If there is still no initiative, stop and tell the person to run `nvb-triage` first. Only
   `nvb-triage` creates an initiative.

---

## Read the configuration

- Read `docs/nvb/config.md`. If it is missing, ask the language question once (R8), recommend `en`,
  then write `config.md` from `templates/config.md`.
- From then on, write headings and content in that language. Keys, IDs and anchors stay in English.

---

## Compute the state of a deliverable

Possible states, from the most to the least blocking:

| State | Condition |
|---|---|
| `not-required` | the approved triage does not require it |
| `absent` | required, but the file does not exist |
| `stale` | R7 staleness applies, whatever the status (draft or approved) |
| `open-questions (n)` | `status: draft` with `n` open questions |
| `draft` | `status: draft`, no open question |
| `approved-invalid` | `status: approved` but R2 gate conditions 2–4 fail |
| `approved` | approved and fresh (R2 gate) |

For an ADR, read "approved" as "in force" (R2). Derogations are not a state. They are shown
separately on every dashboard.

To compute the state:

1. Parse the front matter strictly. If the YAML is invalid or a key is missing, the state is
   `approved-invalid` if the file claims `approved`, otherwise `draft`, with the note "front matter
   invalid: <key>".
2. Count the items of the form `**Q-xx**` in the section marked `<!-- nvb:open-questions -->`. Note
   the ones that carry an R3 prefix (`Reclassification:`, `ADR required:`…), because another skill
   settles them.
3. Check that every section marked as mandatory in the template has content (R4).
4. Check the coverage table, if any (R6).
5. Check staleness (R7) against the current upstream versions, ADR statuses and the
   `system/system.md` section map.

---

## Check the upstream gate

Apply R2 "Gate check". The upstream of each skill is:

| Skill | Upstream that must be approved and fresh (only those the triage requires) |
|---|---|
| `nvb-prd` | triage |
| `nvb-research` | triage, prd |
| `nvb-prototype` | triage, prd, research |
| `nvb-adr` | triage (the decision always belongs to an initiative) |
| `nvb-design` (delta) | triage, prd, research, prototype, and `system/system.md` (not needed when the repository is greenfield: `system@0`) |
| `nvb-design` (bootstrap) | triage |
| `nvb-design` (system continue) | — (`system.md` is a draft: continue the bootstrap or report it came from) |
| `nvb-plan` (plan) | triage, prd, research, prototype, design, every ADR the design cites (in force) |
| `nvb-plan` (direct task) | triage, research, prototype |
| `nvb-design` (report mode) | design, plan, and every task in state `done` or `removed` |

The initiative must also have **no pending reclassification question** in any of its deliverables
(R9: pending means that no triage change log row names it yet). A settled reclassification
question does not block the gate. The skill that raised it closes it.

The gate never counts the deliverable the skill is about to write. Its own open questions and its
own staleness are the reason the skill is running.

If the gate fails, print this table and stop:

```
Gate failed for nvb-design on N-007-auth-tokens:
  prd.md        open-questions (2)   → run /nvb:nvb-prd N-007
  research.md   stale (prd@2)        → run /nvb:nvb-research N-007
```

---

## Next initiative number

The number is never reused, even when the folder has been deleted or the work happened on another
branch. Collect every number already in use:

1. Run `git fetch --prune --quiet`. If it fails (no remote, no network), say so and continue with
   local data, but warn that a conflict with another person remains possible.
2. Collect the folders `docs/nvb/N-*` in the working tree.
3. Collect every remote branch name that matches `N-\d{3,}`:
   `git branch -r --format="%(refname:short)"`.
4. For each remote branch, collect the folders `docs/nvb/N-*`:
   `git ls-tree -d --name-only <branch> docs/nvb/`.
5. Collect the numbers that git history mentions under `docs/nvb/`, to catch deleted folders:
   `git log --all --name-only --format= -- docs/nvb/ | grep -oE "N-[0-9]{3,}"`.

The new number is the maximum of all these numbers + 1, padded to 3 digits. If nothing is found, the
first number is `N-001`. Show the person the sources that were read and the number chosen.

---

## Start a new version

This procedure is used when an approved deliverable has to change, whatever the reason: new answer,
upstream change, reclassification.

1. `version` +1, `status: draft`, `approved_by: ""`, `approved_on: ""`.
2. Change only what the interview settled.
3. Add a change log row listing the added, modified and removed IDs.
4. Update `covers` to the upstream versions that were actually reviewed.
5. Tell the person which downstream deliverables will become stale (R7).

A **draft** deliverable is edited in place without a new version. Its version only changes once it
has been approved.

---

## Delta review (stale deliverable)

1. For each stale fingerprint in `covers`, read the upstream change log rows between the cited version
   (exclusive) and the current version (inclusive).
2. Build the list of added, modified and removed IDs. For a stale system citation, use the changed
   sections instead. For a superseded ADR, use the ADR and its successor.
3. Interview the person **only** on those IDs (R3). For each one, the question is: does it change
   anything in this deliverable?
4. Update the affected sections and the coverage table. A removed upstream ID removes its coverage
   row, and the downstream elements that existed only for it become candidates for removal: ask.
   **ADRs**: for each ADR the deliverable cites, check whether its "Context" cites a changed ID. If it
   does, ask whether the decision still holds. If it does not, record an open question
   `ADR supersession required: ADR-xxxx — <reason>`. An ADR's `decided_against` is informational, so
   an ADR never becomes stale by itself.
   **Settled reclassification questions** (R9): remove them, and record the outcome where it
   belongs.
5. Update `covers`. If the deliverable was approved, apply "Start a new version". If it is a draft,
   edit it in place.

---

## Hand over to the person

At the end of every run, print exactly these elements, in this order:

1. The file(s) written, with their state (see "Compute the state of a deliverable").
2. The open questions, if any. Each one is quoted with its ID, and the ones with an R3 prefix name
   the skill that settles them.
3. If the deliverable can be approved, the exact lines the person has to edit themselves:

   - initiative deliverable, direct task, `system.md`:

     ```yaml
     status: approved
     approved_by: <your name>
     approved_on: <YYYY-MM-DD>
     ```

   - ADR (`accepted` for a new ADR, `revised` for a correction):

     ```yaml
     status: accepted
     decided_by: <your name>
     decided_on: <YYYY-MM-DD>
     ```

     plus a History row: `| <YYYY-MM-DD> | accepted | <your name> | |`.

   - task, after reviewing a `verified` task: `state: done`.

4. The next skill to run, as `nvb-triage` would compute it.

Never write these values yourself, even when the person says "go ahead" (R2).
