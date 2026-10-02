---
name: nvb-plan
description: Breaks an nvb initiative into bounded tasks. For complex features and new products, it writes plan.md (ordered tasks, dependencies, scopes, coverage of every REQ, SEC, DES and OPS as realised and verified, adversarial review), and after the person approves it, one task file per task in docs/nvb/N-xxx-slug/tasks/T-xx-slug.md. That file is the Build agent's brief, with objective, IDs implemented, precise sections to read, scope, executable success criteria, things to avoid, dependencies, state and source fingerprints. For a bug fix or a small feature, it writes a single direct task instead. Parallel tasks require no mutual dependency and disjoint file scopes. Use when asked to plan, break down, split into tasks, create tickets or the backlog of an nvb initiative, to write the task for an nvb bug fix or small feature, or when nvb-triage names nvb-plan as the next action.
argument-hint: "[N-xxx]"
---

# nvb-plan

Read before anything else:

- `${CLAUDE_PLUGIN_ROOT}/references/rules.md`
- `${CLAUDE_PLUGIN_ROOT}/references/procedures.md`
- `${CLAUDE_PLUGIN_ROOT}/references/templates/plan.md`
- `${CLAUDE_PLUGIN_ROOT}/references/templates/task.md`
- `${CLAUDE_PLUGIN_ROOT}/references/templates/task-direct.md`

## Input contract

- **Argument**: the initiative (`N-xxx`), or nothing (procedure "Locate the initiative").
- **Mode selection**:

  | Situation | Mode |
  |---|---|
  | the triage requires `direct-task` (bug-fix, small-feature) | **Direct task** |
  | `plan.md` absent, draft or stale | **Plan** (create, continue or delta review) |
  | `plan.md` approved and fresh, task files absent or derived from an older plan version | **Generate tasks** |
  | a task in `state: reclassify`, after `nvb-triage` sent the person here | **Plan**, with a new version that fixes the task's scope or splits it |
  | `plan.md` approved, and the person asks for a change | **Plan**, with a new version |

- **Required, approved and fresh**: see the procedure "Check the upstream gate". For a plan, the
  design and every ADR it cites must be in force (R2).

Apply the procedure "Check the upstream gate". If it fails, stop.

## Bounded task: all four conditions are required

- **(a)** One observable outcome, checkable by a test or a command.
- **(b)** An announced scope of files or components (paths or globs).
- **(c)** Reviewable in a single review.
- **(d)** It implements at least one `REQ`, `SEC`, `DES` or `OPS`, or it is marked `technical` with
  its justification. A direct task meets (d) by citing `triage`.

The size limit is **indicative**: about 300 changed lines or 5 files. Above it, ask a split question
(R3) with a proposed split. The person may keep the task whole, and their reason is recorded in the
task row.

---

## Mode: Plan

1. **Read** the design (all `DES` and `OPS`, the verification strategy), the PRD (`REQ`, `SEC`), the
   ADRs (their consequences become "Avoid" items), the prototype's "What must not be carried over",
   and the code within the planned scopes.
2. **Propose the full ordered breakdown** as a table, following the template: ID, outcome,
   implements, depends on, scope, type, size. You may propose it, because the plan is reviewed
   before it is approved. Then interview **one question at a time** (R3) on:
   - every interface shared by two tasks or more. Propose a dedicated task that freezes it first,
     with the others depending on it;
   - every task above the size limit (split question);
   - every `technical` task, to get its justification;
   - every `OPS` that no task takes. Ask who receives it, and where it is recorded (Deploy, Monitor);
   - every gap in the design that blocks the breakdown (a `DES` with no verifiable outcome, a
     missing interface). Record it as an open question `Design change required: DES-xx — <gap>`,
     which a new design version (`nvb-design`) settles;
   - the order, when two orders are possible.
3. **Parallelism**: two tasks may run in parallel only if neither depends on the other **and**
   their scopes are disjoint. Check every pair of tasks without dependency. If their scopes overlap,
   either add a dependency or change the scopes. Do not write waves in the plan. `nvb-triage`
   computes them.
4. **Coverage**: every `REQ` and `SEC` is **realised and verified** by at least one task (the
   verifying task has a success criterion that observes it). Every `DES` is realised. Every `OPS` is
   realised or handed over explicitly. An uncovered row shows `—` and points to an open question
   (R6).
5. **Adversarial review**. Start **one** reviewer subagent (Agent tool, `general-purpose`) with a
   fresh context. Give it only the paths of `prd.md`, `design.md`, `plan.md` and the ADRs, and ask
   for findings with evidence, without writing any file:
   - partial realisation: a requirement split across tasks, but with a part in none of them;
   - a task without an executable check, or a `SEC` checked only on the happy path;
   - circular or missing dependencies;
   - parallel tasks with overlapping scopes;
   - a task citing nothing, a forgotten `OPS`, a task that contradicts an ADR.

   Present each finding to the person, one at a time. It leads to a change (task ID), an open
   question, or a rejection with the person's reason. Record them in "Adversarial review". Say that
   this review is not independent evidence: it comes from the same model family.
6. **Write** `plan.md`, `status: draft`, with `covers`, `adrs` and `system_sections` (copied from the
   design). Hand over (procedure). After the
   person approves it, the next step is `/nvb:nvb-plan N-xxx` again, to generate the task files.

### Challenge points

- A task whose outcome is "implement X" with no observable result. Ask what a reviewer would run.
- A "write tests for everything" task at the end. Verification belongs with each task.
- A task whose scope is "the whole module".
- A `SEC` realised but verified by no task.
- A task that implements a `DES` from a design version other than the one in `covers`.

---

## Mode: Generate tasks

1. For each task of the approved plan, write `tasks/T-xx-<slug>.md` from `templates/task.md`:
   - `derived_from: plan@<n>`, plus `implements`, `depends_on`, `scope` and `type`, copied from the
     plan;
   - `covers`: the fingerprints of the sources quoted (`prd@2`, `design@1`, `system@4`), `adrs`, and
     `system_sections` (the system sections the task reads, a subset of the plan's);
   - "Implements": the quoted text of each ID;
   - "Read first": **precise** sections (`design.md#interfaces` plus the `DES` IDs), never "read
     the design";
   - "Success criteria": executable. Use the verification strategy of the design for each ID this
     task verifies;
   - "Avoid": prohibitions quoted from ADR consequences, `SEC`s, the design and the prototype's
     "not carried over" list, each with its source ID;
   - `state: todo`.
2. A task file **adds nothing** to the plan. If writing it reveals a gap (no executable criterion,
   a section that is missing), stop. Tell the person that the plan needs a new version. Do not fill
   the gap in the task.
3. **After a new plan version**: regenerate only the tasks listed as added or modified in the plan's
   change log, and update `derived_from` on the others. Do not reset the `state` of an unchanged
   task.
   - A modified task in `reclassify` returns to `in-progress`: the work already done is kept, and the
     Build checks it against the new scope.
   - A modified task already `verified` or `done`: ask the person whether it goes back to `todo`.
   - A removed task keeps its file, with `state: removed` and a line saying it was removed in
     `plan@<n>`. A removed task never blocks a dependency, a report or the closure of the
     initiative. A task that depended on it must be modified by the same plan version.
4. Hand over: list the tasks, and the first tasks that can start (no `depends_on`).

---

## Mode: Direct task (bug-fix, small-feature)

No `plan.md`: a single task `T-01`, approved by the person.

1. Interview, one question at a time (R3):
   - **bug-fix**: the expected behaviour; the reproduction. Attempt it yourself when it is safe and
     read-only, and record the command and the observed result. Then the test that will fail before
     the fix and pass after it (its name and location; writing it belongs to the Build);
   - **small-feature**: `REQ-xx` with testable acceptance criteria, as in the PRD;
   - for both: `SEC-xx` when the triage has a derogation on a `TQ-S` question (at least one, as
     `triage-questions.md` requires); scope in and out; executable success criteria; things to
     avoid.
2. Check that it is a bounded task (a–d). If it is not (several outcomes, a large scope), raise a
   `Reclassification:` question (R9): the level is probably too low.
3. Coverage: fill the "Coverage" section, so that each `REQ`/`SEC` is verified by a success
   criterion (R6).
4. Write `tasks/T-01-<slug>.md` from `templates/task-direct.md` (`status: draft`, `state: todo`).
   Hand over: the person approves the task.

---

## Build contract (for reference; the Build is outside this skill)

- States: `todo` → `in-progress` → `verified` → `done`, plus `reclassify` and `removed`. The agent
  writes `verified` when every success criterion passes, and pastes the outputs into
  "Execution log". **Only the person** writes `done`, after review. Only `done` unblocks the
  dependent tasks. `nvb-plan` writes `todo` and `removed`.
- A diff that leaves the announced `scope` does not become `verified`. It becomes `reclassify`, and
  `nvb-triage` takes over. Then either the triage changes, or a new plan version fixes the scope.
- The IDs are the contract: a change that serves no ID in `implements` is out of design.
- No summarised `brief.md`. The task file points to the sources, so that it cannot diverge from them.

## Never

- Write `approved` or `done` (R2), or modify the design, PRD or ADRs (R6).
- Edit an approved `plan.md` to track progress.
- Put in a task file a requirement, a decision or a scope that the plan and its sources do not
  contain.
