---
name: nvb-prototype
description: Runs a throwaway prototype for an nvb initiative, to answer a feasibility or interaction question that only running code can settle. The question is written and confirmed first. The code lives on a branch nvb/proto/N-xxx-slug that is never merged, and prototype.md on the main line records the answer, the PRO-xx findings, the commit hash, the exact commands and their outputs, so that the branch can be deleted without losing the proof. The deliverable is the lesson, never the code. Use when asked to prototype, spike, try out, test feasibility or build a proof of concept for an nvb initiative, or when nvb-triage names nvb-prototype as the next action.
argument-hint: "[N-xxx]"
---

# nvb-prototype

Read before anything else:

- `${CLAUDE_PLUGIN_ROOT}/references/rules.md`
- `${CLAUDE_PLUGIN_ROOT}/references/procedures.md`
- `${CLAUDE_PLUGIN_ROOT}/references/templates/prototype.md`

## Input contract

- **Argument**: the initiative (`N-xxx`), or nothing (procedure "Locate the initiative").
- **Required, approved and fresh**: `triage.md` with `prototype` in `required` (from `TQ-U2`), plus
  `prd.md` and `research.md` when the triage requires them. If a prototype is not required, stop.
- **Read**: the `TQ-U2` evidence of the triage, the "Questions for the prototype" of `research.md`,
  and the PRD IDs involved.
- **Environment**: a git repository with a clean working tree. If the tree is not clean, stop and
  ask the person to commit or stash. Never do it for them.

Apply the procedure "Check the upstream gate". If it fails, stop.

## Modes

| State of `prototype.md` | Mode |
|---|---|
| `absent` | **Create**: all the steps below |
| `draft` / `open-questions` | **Continue**: resume at the first step not done. Reuse the branch `nvb/proto/N-xxx-*` and its worktree if they exist. If they do not exist, check out the branch again from its last commit (`commit` in the front matter) |
| `stale` | **Delta review** (procedure). For each changed upstream ID, ask whether it changes a question or invalidates a run. Rerun only what is invalidated, on the same branch, with a new commit and new evidence |
| `approved`, and the person asks for a change | **New version** (procedure "Start a new version"), then the steps needed for the new or changed questions |

## Steps

1. **The question first** (R3, one question at a time). Propose the questions, starting from the
   research and the triage. Each one must be answerable by yes, no or a measurement, and state what
   result answers each way. The person confirms them. **No code before this confirmation.** Write
   the "Question" section of `prototype.md` immediately (`status: draft`).
2. **Isolate the code.** Propose the git worktree below, and create it only after the person agrees:
   ```
   git worktree add ../<repo>-proto-N-xxx -b nvb/proto/N-xxx-<slug>
   ```
   This keeps the main working tree untouched. Pushing the branch is optional, and only if the
   person asks.
3. **Write the minimum code** that answers the question, and nothing more. Shortcuts are allowed
   (no error handling, hard-coded values, no tests), but **each one is listed** for the section
   "What must not be carried over". Never put a real secret in the code, even temporarily. Use an
   environment variable or a dummy value.
4. **Run it** and capture each command exactly as typed, with its output. Record the environment:
   OS, runtime and library versions (`dotnet --info`, `node -v`, lock file…).
5. **Commit** on the prototype branch, and record the full hash.
6. **Write `prototype.md` on the main line**, in the initiative folder of the original working
   tree, **not** on the prototype branch: Answer, `PRO-xx` findings each pointing to its run,
   Evidence (commands and outputs trimmed with "[…]"), and "What must not be carried over".
7. **Present the answer** to the person. If a finding contradicts a `REQ`/`SEC` of the PRD (infeasible
   as stated, a threshold that cannot be reached), record it as a `PRO`, plus an open question
   `PRD change required: REQ-xx — <finding>`, settled by a new version of the PRD (`nvb-prd`). The
   prototype then becomes stale, and its delta review closes the question. If it reveals a fact that answers
   "yes" to a triage question, raise a reclassification question (R9).
8. **Clean up**: propose to remove the worktree (`git worktree remove`). Deleting the branch is the
   person's decision. Check first that `prototype.md` holds everything needed.

### Challenge points

- A question that is really a design choice ("which library is better?"). Turn it into a measurable
  question, or send it back to the design.
- A result obtained in conditions far from the target (data volume, OS, network). Record the gap
  in the finding.
- A "yes" that rests on a single run when the behaviour may vary. Ask whether to repeat it.
- The temptation to keep the code: refuse it, and say why (the deliverable is the lesson).

## Write

- Use `covers` with the upstream deliverables that exist, and record `branch` and `commit` in the
  front matter.
- Write in the repository language (R8). Commands and outputs stay as they are.

## Hand over

Apply the procedure "Hand over to the person". The next skill is usually `nvb-design`.

## Never

- Write `approved` (R2), or modify the PRD, the research or the triage (R6).
- Merge, cherry-pick or copy prototype code into the main line, or reference it from a task.
- Write a finding that no recorded run supports.
