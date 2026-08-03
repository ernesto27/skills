# TASKS.md — {{SOURCE_LANG}} → {{TARGET_LANG}} port work queue

The work queue **and** the progress ledger. One row per unit of work, ordered by
dependency depth: leaves first, so nothing is ever ported against a target that does not
exist yet.

**This file is updated at the end of every task, in the same session that did the work.**
A task is not finished until its row says so and its ledger entries are written.

- Legend: `TODO` not started · `DOING` claimed, in progress · `BLOCKED` waiting on
  something named in Notes · `REVIEW` translated, awaiting review findings · `DONE`
  translated, reviewed, fixed, tested, green.
- **Never mark a row `DONE` without the four ledger lines** (port / review / fix / test)
  in §2 and a passing test run. A row is `DONE` only when the tests you can run are green
  and every review finding is either applied or rejected in writing.
- Never delete a row. Cancelled work becomes `DONE` with `cancelled: <reason>` in Notes.
- Discovering new work mid-task means adding a row, not silently widening the current one.

---

## 1. Queue

### Wave 0 — trial run
<!-- 2-3 representative files, hand-driven. The deliverable of this wave is an amended
     PORTING.md, not just the files. -->

| ID | Status | Source | Target | LOC | Depends on | Notes |
|---|---|---|---|---|---|---|
| T1 | TODO | | | | — | |

### Wave 1 — scaffold
<!-- Project skeleton, build config, CI wiring, dependency set, harness. No business logic.
     Gate: the target builds and the (empty) test command runs. -->

| ID | Status | Source | Target | LOC | Depends on | Notes |
|---|---|---|---|---|---|---|
| | | | | | | |

### Wave 2 — {{LAYER_NAME}}
<!-- One wave per dependency layer. Name them after the codebase's own layers. -->

| ID | Status | Source | Target | LOC | Depends on | Notes |
|---|---|---|---|---|---|---|
| | | | | | | |

---

## 2. Ledger

Append-only, newest last. `port`, `review`, `fix`, and `test` stay **separate entries** —
that separation is what makes review yield measurable. Never fold them into one line.

```
[T12] port      src/models/notification.py ← backend/models/notificacion.go (122 LOC, reads + writes)
[T12] review    src/models/notification.py — 3 findings: R22 log-and-continue inverted in the
                recipients loop; R19 NULL email surfaced as None instead of ""; R34 whitespace
                changed inside the UPDATE
[T12] fix       findings 1-2 applied; 3 rejected — the SQL text is byte-identical, only the
                Python string-literal indentation differs (models/notificacion.go:88)
[T12] test      tests/models/test_notification.py 9/9 · full suite 61/61
[T12] DONE      no new stubs · FINDINGS: +1 source defect (F7)
```

Entry types: `port` · `review` · `fix` · `test` · `DONE` · `BLOCKED` · `prep` (scaffolding)
· `verify` (an empirical check of a PORTING.md assumption) · `doc` · `amend` (a PORTING.md
rule changed, with the rule ID and why).

---

## 3. Metrics

Updated at the end of each wave.

| Metric | Value | Notes |
|---|---|---|
| Files ported / total | 0 / {{TOTAL_FILES}} | |
| Tests green / total | 0 / {{TOTAL_TESTS}} | |
| Open `TODO(port)` stubs | 0 | Each must have an open question in FINDINGS.md |
| Escape hatches ({{ESCAPE_HATCHES}}) | 0 | Burn down after cutover, not during |
| Review findings raised / applied | 0 / 0 | If yield collapses, the reviewer lost context separation |
| Test deltas unclassified | 0 | **Must be 0 at every wave gate** |
| Open questions unanswered | 0 | |
