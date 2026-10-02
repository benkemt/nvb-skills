---
name: nvb-prd
description: Writes the PRD of an nvb initiative (docs/nvb/N-xxx-slug/prd.md) by interviewing the person one question at a time. It covers context and problem, actors, REQ-xx requirements each with a testable acceptance criterion, mandatory SEC-xx security requirements, NFR-xx only when expressed, constraints, explicit out of scope and success criteria. Writes only what the person said, turns every unsettled point into a blocking open question, and runs an adversarial review for new products. Also revises a PRD (new version) and reviews a stale PRD after a triage change. Use when asked to write, draft, continue, revise or update the PRD, product requirements or requirements of an nvb initiative, or when nvb-triage names nvb-prd as the next action.
argument-hint: "[N-xxx]"
---

# nvb-prd

Read before anything else:

- `${CLAUDE_PLUGIN_ROOT}/references/rules.md`
- `${CLAUDE_PLUGIN_ROOT}/references/procedures.md`
- `${CLAUDE_PLUGIN_ROOT}/references/templates/prd.md`

## Input contract

- **Argument**: the initiative (`N-xxx`), or nothing (procedure "Locate the initiative").
- **Required, approved and fresh**: `triage.md` with `prd` in `required`. If the triage does not
  require a PRD, stop and say so. A bug fix or small feature has its requirements in the direct task
  (`nvb-plan`).
- **Read**: the triage (request, answers with evidence, derogations), and the code the triage
  evidence points to.

Apply the procedure "Check the upstream gate". If it fails, stop.

## Modes

Pick the mode from the state of `prd.md`:

| State of `prd.md` | Mode |
|---|---|
| `absent` | **Create**: full interview |
| `draft` / `open-questions` | **Continue**: interview only on open questions and empty sections |
| `stale` | **Delta review** (procedure), only on the changed triage questions |
| `approved`, and the person asks for a change | **New version** (procedure "Start a new version"), then interview only on what changes |

## Interview (R3)

One question at a time. Each question comes with a recommendation and its reason. Follow this order,
and skip what is already settled:

1. **Context and problem**: what exists, what goes wrong, for whom, why now. Start from the "Request"
   section of the triage.
2. **Users and actors**: humans, systems, teams. One line each.
3. **Requirements**: one `REQ` at a time. For each one, ask for its **acceptance criterion**: "How
   will we check it is done, with a test or a command?" Propose a criterion, and let the person
   correct it.
4. **Security requirements**: driven by the `TQ-S` answers of the triage. For each `TQ-S` answered
   yes, at least one `SEC` (R4). Then go through the checklist, one point at a time, skipping those
   that clearly do not apply: data classification and protection; who may do what (roles,
   ownership); exposure and input validation; secrets; security events to log; abuse cases (what
   a malicious or careless actor would try). Each `SEC` has an acceptance criterion that includes
   the abuse case ("a user without role X gets 403 on …").
   If no `TQ-S` is yes and nothing applies, ask the person to confirm the sentence
   "Not applicable, because …" and to give the reason.
5. **Non-functional requirements**: ask once: "Is there a constraint on performance, volume,
   availability, compatibility or accessibility?" Record only what is expressed, with a measurable
   threshold. Never propose an NFR the person did not raise. If nothing is expressed, write
   "None expressed."
6. **Constraints**: imposed technology, deadline, regulation, compatibility.
7. **Out of scope**: propose the items a reader could expect (extensions of the requirements,
   neighbouring features), and ask for each one: in or out?
8. **Success criteria**: how we will know, after delivery, that the problem is solved.

### Challenge points

- **"How" disguised as "what"**: the person names a solution (a table, a library, an endpoint). Ask
  whether it is **imposed** (it then goes into Constraints) or a **preference**. A preference stays
  out of the PRD and will be asked again at design time. Say so.
- **Untestable criterion** ("fast", "secure", "user-friendly", "robust"): ask for a number, an
  observable behaviour or a command.
- **Contradiction** with the code, the triage or another `REQ`: explain both sides in plain words,
  each followed by its reference (full file path and line, or `REQ-xx` with its text), and ask which
  one wins.
- **Scope creep**: a requirement goes beyond the triage "Request". Ask whether the scope grows. If
  it does, and the triage answers change (for example, a new exposure), raise a reclassification
  question (R9).
- **Missing actor**: a requirement mentions someone or something absent from "Users and actors".
- **Duplicate or overlap** between two `REQ`s: propose merging them or separating them clearly.
- **A requirement with no problem behind it**: ask which part of the context it solves.

## Adversarial review (only when `adversarial_review: required`)

Once the interview is complete and the draft is written:

1. Start **one** reviewer subagent (Agent tool, `general-purpose`) with a fresh context. Give it
   **only** the paths of `triage.md` and `prd.md` and the repository root. Do not give it the
   interview, and do not tell it what you think. Ask it to find, with evidence:
   - ambiguous or untestable `REQ`/`SEC`/`NFR`;
   - missing actors, missing abuse cases, missing error or edge situations;
   - contradictions between requirements, with the triage or with the code;
   - hidden scope (a requirement that implies more than it says), and missing out-of-scope items.

   The answer must be a list of findings: ID or section, problem, evidence, suggested question.
   The subagent must not write any file.
2. Present the findings to the person **one at a time**. For each one, the person chooses:
   - change the PRD (record the ID changed);
   - turn it into an open question;
   - reject it, with the person's reason.
3. Record every finding and its outcome in the "Adversarial review" section.
4. Tell the person plainly that this review comes from the same model family. It finds gaps, but it
   is not independent evidence. The approval remains theirs.

## Write

- Fill in the template in the repository language (R8). Use `covers: [triage@<n>]`. Number the IDs
  sequentially. Never reuse an ID, even one removed in a previous version (R5).
- Read back each requirement in the interview language before writing it, if the two languages
  differ (R8).
- Write only what was said or confirmed. Every unsettled point becomes a `Q-xx` (R3).
- Before handing over, check: every `REQ`/`SEC` has an acceptance criterion; the security section is
  not empty; out of scope is not empty; the front matter is valid. List any failure as an open
  question.

## Hand over

Apply the procedure "Hand over to the person". Usually, the next skill is `nvb-research` or
`nvb-prototype` if the triage requires it, otherwise `nvb-design`.

## Never

- Write `approved` (R2), or modify `triage.md` (R6). A triage problem is a reclassification question.
- Invent an NFR, a constraint, an actor or an acceptance criterion the person did not confirm.
- Write user stories or detailed personas.
- Put design decisions in the PRD.
