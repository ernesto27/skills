# FINDINGS.md — {{SOURCE_LANG}} → {{TARGET_LANG}} port

Everything the port learned that is not code. Four sections, each append-only, each with
its own ID prefix. **Never delete a row.** Answered questions get an answer appended, not
removed — the reasoning is the value.

| Section | Prefix | What goes here |
|---|---|---|
| 1. Open questions | `Q` | Anything you could not resolve from the source alone. **Blocks the row that raised it.** |
| 2. Source defects | `F` | Bugs in the original that the port reproduces faithfully (R3). |
| 3. Divergences discovered | `D` | Semantic differences found during the port, not caught in the pre-audit. Each one also gets a row in `PORTING.md` §6. |
| 4. Test deltas | `X` | Every test whose assertion had to change, classified (R38). |

Write the row **at the moment of doubt**, not at the end of the task. A doubt you resolve
by guessing and never record is exactly the failure this file exists to prevent.

---

## 1. Open questions

Raise one whenever: the source's intent is genuinely ambiguous · the target has no
equivalent construct · two source call sites contradict each other · a `PORTING.md` rule
does not cover the case or contradicts another rule · you are about to guess.

**A question is not a substitute for the work.** Raise it, stub the spot with
`TODO(port): <reason>` per R5, finish everything in the task that does not depend on the
answer, and mark the task `BLOCKED` only if nothing else can proceed.

| ID | Raised by (task) | Where | Question | Status | Answer |
|---|---|---|---|---|---|
| Q1 | | `path:line` | | OPEN | |

Status: `OPEN` · `ANSWERED` (answer in the last column, and the rule it produced) ·
`DEFERRED` (with the date it must be revisited).

---

## 2. Source defects

Rule R3: **none of these are fixed during the port.** Each is reproduced as-is so the
target can be validated against the original. Fix them after cutover, deliberately, with
their own tests.

### F1 — {{one-line title}}
`{{source path:line}}`

```{{SOURCE_EXT}}
// the offending source, quoted exactly
```

**What actually happens:** …

**Port must reproduce:** … *(be specific enough that a reviewer can check it: the exact
status code, the exact empty body, the exact wrong value)*

**Ported at:** `{{target path:line}}` · task `{{T#}}`

---

## 3. Divergences discovered

Semantic divergences the Phase 1 pre-audit missed. Every row here means the catalog pass
had a gap — add the row to `PORTING.md` §6 and note the category so future ports of this
pair inherit it.

| ID | Category | Where found | {{SOURCE_LANG}} behavior | {{TARGET_LANG}} behavior | Fix applied | PORTING.md rule |
|---|---|---|---|---|---|---|
| D1 | | | | | | |

---

## 4. Test deltas

Rule R38. Every changed assertion, classified. **Unclassified rows must be zero at every
wave gate.**

| ID | Test | What changed | Classification | Justification |
|---|---|---|---|---|
| X1 | | | `port defect` / `source implementation detail` | |

- `port defect` — the target is wrong. Fix the target, revert the test.
- `source implementation detail` — the test asserted something specific to
  {{SOURCE_LANG}} that carries no contract meaning. Say **what** the detail was and
  **why** it carries no meaning. "It's just an implementation detail" is not a
  justification; name the detail.
