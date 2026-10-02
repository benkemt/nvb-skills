# nvb — templates

How to read these templates:

- `{{…}}` is a placeholder, and the skill replaces it. HTML comments starting with `<!-- guide:` are
  instructions for the skill. They are removed from the written deliverable.
- `<!-- nvb:<anchor> -->` marks a section. The anchor is stable, never translated, and kept in the
  written deliverable, so that a script can find the section whatever the language (R8).
- `<!-- nvb:<anchor> mandatory -->` marks a mandatory section. If it is empty, approval is blocked
  (R2, R4).
- Each heading is given as `English | Français`. Write only the one for the repository language.
- No template sets `approved`, `accepted`, `revised` or `done` as the value of a field. Comments that
  explain who writes them are allowed. Only the person writes those values (R2).

| Template | Written by | Path in the target repository |
|---|---|---|
| `config.md` | first skill run | `docs/nvb/config.md` |
| `triage.md` | `nvb-triage` | `docs/nvb/<initiative>/triage.md` |
| `prd.md` | `nvb-prd` | `docs/nvb/<initiative>/prd.md` |
| `research.md` | `nvb-research` | `docs/nvb/<initiative>/research.md` |
| `prototype.md` | `nvb-prototype` | `docs/nvb/<initiative>/prototype.md` |
| `adr.md` | `nvb-adr` | `docs/nvb/system/decisions/ADR-xxxx-<slug>.md` |
| `design.md` | `nvb-design` | `docs/nvb/<initiative>/design.md` |
| `system.md` | `nvb-design` (bootstrap, report) | `docs/nvb/system/system.md` + section files |
| `pending.md` | `nvb-design` | `docs/nvb/system/pending.md` |
| `plan.md` | `nvb-plan` | `docs/nvb/<initiative>/plan.md` |
| `task.md` | `nvb-plan` | `docs/nvb/<initiative>/tasks/T-xx-<slug>.md` |
| `task-direct.md` | `nvb-plan` (bug-fix, small-feature) | `docs/nvb/<initiative>/tasks/T-01-<slug>.md` |
