# nvb — no vibe coding

A Claude Code plugin that covers the **Requirements**, **Design** and **Plan** phases. Each phase
produces a reviewable Markdown deliverable under `docs/nvb/` in the target repository. The agent
produces each deliverable by interviewing you, and you are the one who approves it.

```
nvb-triage ─► nvb-prd ─► nvb-research ─► nvb-prototype ─► nvb-design (+ nvb-adr) ─► nvb-plan ─► tasks
   ▲                                                                                             │
   └──────────────────────── dashboard: state of every deliverable + next action ◄───────────────┘
```

| Skill | Role |
|---|---|
| `/nvb:nvb-triage` | Entry point and dashboard. Classifies the change, assigns `N-xxx`, and shows the state of each deliverable and the next action. It never runs the other skills. |
| `/nvb:nvb-prd` | PRD by interview: `REQ`, `SEC` (mandatory), `NFR`, out of scope. |
| `/nvb:nvb-research` | `research.md`: `RES` findings with source, version, date and exact quote. |
| `/nvb:nvb-prototype` | Throwaway prototype on `nvb/proto/N-xxx-slug`, never merged. The deliverable is the lesson. |
| `/nvb:nvb-adr` | System decisions: eligibility test, revision, supersession. |
| `/nvb:nvb-design` | Delta design against `system@N`, plus the **bootstrap** and **report** modes. |
| `/nvb:nvb-plan` | `plan.md`, then one file per task. For a bug fix or small feature, a single direct task. |

## Principles

- **Only you write `approved`** (and `accepted` / `revised` for an ADR, and `done` for a task). The
  skills check it, and refuse to start when an upstream deliverable is not approved and fresh.
- **Interview first**: one question at a time, each with a recommendation. A deliverable contains
  only what you said. Whatever is unsettled becomes an open question, and an open question blocks
  approval.
- **Security is never empty**: when it does not apply, write "Not applicable, because…".
- **Stable IDs and traceability**: the downstream deliverable covers every upstream ID. When an
  upstream deliverable changes, the downstream one becomes `stale`, and only the changed IDs are
  reviewed.

The rules are in [`references/rules.md`](references/rules.md) (R1–R9), the shared procedures in
[`references/procedures.md`](references/procedures.md), and the templates in
[`references/templates/`](references/templates/).

## Install and test locally

```bash
claude plugin validate .                 # check the manifests
claude --plugin-dir D:/Source/Repos/nvb-skills   # load it for one session
# or, permanently:
/plugin marketplace add D:/Source/Repos/nvb-skills
/plugin install nvb@nvb-local
```

After editing a skill, run `/reload-plugins` in the session.

## Known limits (v1)

- **No mechanical checks**: front matter, coverage, staleness and number conflicts are checked by
  the agent reading the files. The agent therefore checks its own work, which is the weakest point
  of the system. The formats are strict (YAML, regular IDs, `<!-- nvb:… -->` anchors), so that a
  script can take over these checks later.
- The person who answers the interview is often the one who approves. The workflow guarantees that
  a person decides. It does not guarantee a second pair of eyes.
- No tracker mirror, no compliance profile, and a fixed list of triage questions.
