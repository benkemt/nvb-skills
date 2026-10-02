---
name: nvb-adr
description: Records system-level architecture decisions for nvb as ADR files in docs/nvb/system/decisions/ADR-xxxx-slug.md, through an interview. A decision becomes an ADR only if it passes the eligibility test (hard or costly to undo, at least one real alternative considered, why and why-not written), and an ADR is always imposed when the triage answered yes to a security or irreversibility question. Handles the life cycle. An accepted ADR is immutable in substance, a changed decision is superseded by a new ADR with two-way links, and a correction without a decision change becomes a revision with a history entry. Use when asked to record, write, add, revise, correct, supersede or replace an ADR or an architecture decision in an nvb repository, when a design needs an ADR, or when nvb-triage names nvb-adr as the next action.
argument-hint: "[N-xxx] [new | revise ADR-xxxx | supersede ADR-xxxx] [subject]"
---

# nvb-adr

Read before anything else:

- `${CLAUDE_PLUGIN_ROOT}/references/rules.md`
- `${CLAUDE_PLUGIN_ROOT}/references/procedures.md`
- `${CLAUDE_PLUGIN_ROOT}/references/triage-questions.md` (imposed ADRs)
- `${CLAUDE_PLUGIN_ROOT}/references/templates/adr.md`

## Input contract

- **Argument**: the initiative (`N-xxx`) and the mode. Without a mode: **New**, or **Supersede** when
  the design has an open question `ADR supersession required: ADR-xxxx`. Without a subject: propose
  the subjects that are pending, from the `adr_imposed_by` of the triage not yet covered by an ADR in
  force, and from the open questions `ADR required:` of the design.
- **Required, approved and fresh**: `triage.md`. Read also the PRD, research and prototype if they
  exist and are approved. A decision may rely on them, never on a draft.
- **Read**: every ADR in `docs/nvb/system/decisions/`, to detect a decision that already exists, a
  contradiction, or a predecessor. Read also the system sections concerned.

Apply the procedure "Check the upstream gate". If it fails, stop.

## Every run: complete the supersession links

Before any mode, look for ADRs in force (R2) whose `supersedes` lists an ADR that is not yet
`superseded`. For each one, update the old ADR: `status: superseded`, `superseded_by`,
and a History row. This is the **only** change a skill may make to an accepted ADR without the
person. Tell the person which deliverables now cite a superseded ADR and become stale (R7).

## Mode: New

1. **Number**: `ADR-xxxx` is global to the repository and never reused. Take the maximum ID found in
   `docs/nvb/system/decisions/`, in the same folder on every remote branch
   (`git ls-tree --name-only <branch> docs/nvb/system/decisions/`), and in git history
   (`git log --all --name-only --format= -- docs/nvb/system/decisions/`), then add 1. Apply the same
   fetch rule as the procedure "Next initiative number".
2. **Eligibility test** (R3, one question at a time). Ask about each condition:
   - **Hard or costly to undo?** Data, published contract, security model, migration cost, other teams.
   - **A real alternative considered?** Name it. A straw man that nobody would choose does not count.
   - **Why and why-not?** The person can state the reason for the choice, and for each rejection.

   If a condition fails and the ADR is **not imposed**, stop. The decision stays in the design, with
   the condition that failed. Tell the person, and write nothing.
   If the ADR is **imposed** (`imposed_by` not empty) and a condition fails, do not stop. Help the
   person find the real alternatives: propose options that are actually credible, with their
   sources. If none can be found, record an open question. The only other way out is a derogation
   of kind `adr`, which the person decides with `/nvb:nvb-triage N-xxx` (Derogation mode). Say so.
3. **Interview**, in this order: context (the forces, with their IDs and sources); the options (at
   least two, chosen included; you may propose options, but only those the person keeps are
   written); the decision, **taken by the person**; the why-not of each rejected option, **in the
   person's words**; the consequences (what becomes harder, what is now forbidden, the cost of
   reversing); the security effect (R4).
4. **Write** `ADR-xxxx-<slug>.md` with `status: proposed` and a History row "created".
5. **Hand over**. The person accepts the ADR by editing it:
   `status: accepted`, `decided_by`, `decided_on`, and a History row "accepted".

### Challenge points

- An existing ADR already decides this, or contradicts it. Then this is a **Supersede**, not a New.
- The decision is driven by a preference stated in the PRD, not by a requirement. Ask which
  `REQ`/`SEC`/`NFR` actually drives it.
- A rejected option was rejected for a reason that is false according to the research or the
  prototype. Quote the `RES`/`PRO`.
- The decision weakens a `SEC`, or answers a `TQ-S` question without addressing it.
- The cost of reversing is described as "low" for a schema, a contract or a security model.

## Mode: Revise `ADR-xxxx`

For a correction **without a change of decision**: wording, clarification, a forgotten consequence
that changes nothing for those who cite it.

1. Test it first: "Would any deliverable citing this ADR have to change?" If yes, or if the decision
   itself changes, this is a **Supersede**. Say so, and switch modes.
2. Apply the correction. Add a History row "revised" that quotes the **previous wording** of each
   changed passage. Set `revision` +1 and `status: revision-proposed`.
3. Hand over. The person reviews and sets `status: revised` in a History row. A revision does not
   make anything stale.

## Mode: Supersede `ADR-xxxx`

1. Same steps as **New**, with `supersedes: [ADR-xxxx]`. The context explains what changed since
   the old decision.
2. Do **not** touch the old ADR now. The link is completed on the first run after the person accepts
   the new one (see "Every run").
3. Tell the person, before they accept, which deliverables cite the old ADR and will become stale.

## Never

- Write `accepted` or `revised`, or fill `decided_by` / `decided_on` (R2).
- Change the substance of an accepted ADR. Changing the decision means a new ADR.
- Reuse or renumber an ADR ID, or delete an ADR file.
- Record as an ADR a rule, a parameter value, a requirement or a convention. Those belong to the
  PRD or the design.
