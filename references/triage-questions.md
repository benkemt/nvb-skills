# nvb — triage questions and levels

`nvb-triage` uses this file, and so does every skill that applies R9 (reclassification).
Every question is closed. The only valid answers are `yes` and `no`, and every answer has a written
effect. A `yes` always comes with its evidence: the fact explained in one sentence, then its
reference (full file path and line). See R3, "Explain, do not point".

---

## Questions

### Security (`TQ-S`)

| ID | Question | Effect if `yes` |
|---|---|---|
| `TQ-S1` | Does the change create, read, store or transmit **personal or sensitive data**? | floor `complex-feature`, `SEC` mandatory, ADR imposed |
| `TQ-S2` | Does it touch **authentication, authorisation or permissions**? | floor `complex-feature`, `SEC` mandatory, ADR imposed |
| `TQ-S3` | Does it create a **new exposure**: endpoint, port, imported file, message input, UI input reaching the server? | floor `complex-feature`, `SEC` mandatory, ADR imposed |
| `TQ-S4` | Does it create, store, use or rotate **secrets or keys**? | floor `complex-feature`, `SEC` mandatory, ADR imposed |

### Irreversibility (`TQ-I`)

| ID | Question | Effect if `yes` |
|---|---|---|
| `TQ-I1` | Does it change a **schema or migrate data**? | floor `complex-feature`, ADR imposed |
| `TQ-I2` | Does it create or change a **published format or contract**: API, file, message, event? | floor `complex-feature`, ADR imposed |
| `TQ-I3` | Does it **delete data or a feature**? | floor `complex-feature`, ADR imposed |

### Unknown (`TQ-U`)

| ID | Question | Effect if `yes` |
|---|---|---|
| `TQ-U1` | Does it use an **API, library or technology never used** in this repository or by this team? | `research` required |
| `TQ-U2` | Is there **uncertainty about feasibility or about an interaction** that only running code can settle? | `prototype` required |
| `TQ-U3` | Does it span **several components or several teams**? | consider level `new-product`, which the person decides |

Only the `TQ-U` questions can make `research` and `prototype` required. The level never does.

---

## Levels

| Level | `prd` | `design` | ADR | `plan` | Tasks |
|---|---|---|---|---|---|
| `bug-fix` | – | – | – | – | 1 direct task |
| `small-feature` | – | – | – | – | 1 direct task, with an embedded "Requirements" section |
| `complex-feature` | ✔ | ✔ | if it passes the ADR test (imposed by any `TQ-S`/`TQ-I` yes) | ✔ | N |
| `new-product` | ✔ + adversarial review | ✔ | if it passes the ADR test (imposed by any `TQ-S`/`TQ-I` yes) | ✔ | N |

- The **floor** is the lowest level allowed by the answers. Any `yes` to `TQ-S*` or `TQ-I*` sets it to
  `complex-feature`. With no `yes`, the floor is `bug-fix`.
- The agent **proposes** a level at or above the floor, with its reason. The person **confirms** it.
- `nvb-adr` has the ADR eligibility test. "ADR imposed" means that the design cannot be approved
  until it cites at least one ADR in force (`accepted`, `revised` or `revision-proposed`) for each `TQ-S`/`TQ-I` question answered `yes`.

---

## Derogation

The person may go below what the answers impose. This is allowed, but it is recorded. There are two
kinds of derogation:

- `level`: a level **below the floor**;
- `adr`: no ADR for a `TQ-S`/`TQ-I` question that imposes one (for example, when the decision fails
  the eligibility test and no real alternative exists).

Each derogation is recorded in the front matter (`derogations: [{question: TQ-S2, kind: level}]`)
and in the "Derogations" section of `triage.md`:

```markdown
- **TQ-S2** (`level`) — level `small-feature` instead of `complex-feature`.
  *Justification (written by the person):* "<their words, verbatim>".
  *Decided by:* <name>, <YYYY-MM-DD>.
```

- The justification is written in the person's own words. The agent does not rephrase it.
- The derogation stays visible for the whole life of the initiative: `nvb-triage` shows it on every
  dashboard, and every task of the initiative quotes it in its "Context" section.
- A `level` derogation on a `TQ-S*` question still requires **at least one `SEC-xx`** in the direct task's
  "Requirements" section.
- A derogation of kind `adr` removes the question from `adr_imposed_by`. The design then says
  "ADR derogated, see triage.md" in its "Decisions" section.
- A derogation decided after the triage is approved means a new version of the triage
  (`nvb-triage`, Derogation mode).
