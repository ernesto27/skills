# PORT-PLAN.md — {{SOURCE_LANG}} → {{TARGET_LANG}}

- **Source:** `{{SOURCE_DIR}}` · {{TOTAL_FILES}} files · {{TOTAL_LOC}} LOC
- **Target:** `{{TARGET_DIR}}`
- **Owner:** {{OWNER}} — the single person who approves rule changes and says "stop".
- **Date:** {{DATE}}
- **Status:** Draft / Approved

Companion documents: [`PORTING.md`](./PORTING.md) (the rules) ·
[`TASKS.md`](./TASKS.md) (the queue and ledger) · [`FINDINGS.md`](./FINDINGS.md)
(questions, source defects, divergences, test deltas).

---

## 0. Size and shape

Measured, not estimated. Record the commands too — these numbers get re-measured at
cutover to show what actually landed, and a number nobody can reproduce is a number
nobody can check.

- **Measured on:** {{DATE}} · source at `{{SOURCE_REF}}`
- **Total:** {{TOTAL_FILES}} files · {{TOTAL_LOC}} LOC
- **Excluding tests:** {{SRC_FILES}} files · {{SRC_LOC}} LOC
- **Tests:** {{TEST_FILES}} files · {{TEST_LOC}} LOC
- **Counted with:** `{{COUNT_COMMAND}}`
<!-- State what the count includes and excludes: generated files, vendored deps,
     migrations, fixtures, templates. Two people counting the same tree differently is
     how a port's progress metric stops meaning anything. -->

### Per area

Drives the wave ordering in §8 and the row count in §5.

| Area | Files | LOC | % of total | Note |
|---|---|---|---|---|
| | | | | |
| **Total** | | | 100% | |

### Largest files

The three largest dominate the schedule and the risk — name them explicitly rather than
letting them surface mid-wave. Each one gets a **high** risk row in §5 by default.

| # | File | LOC | % of total | Has tests | Wave |
|---|---|---|---|---|---|
| 1 | | | | | |
| 2 | | | | | |
| 3 | | | | | |

### Not read

An honest gap in the inventory is useful; a silent one is a landmine.

| Path | Files | LOC | Why not read |
|---|---|---|---|
| | | | |

---

## 1. Goal and non-goals

**Goal.** A mechanical translation of `{{SOURCE_DIR}}` into {{TARGET_LANG}} that
preserves architecture, module boundaries, names, and control flow. The result should
read as if the source were transpiled, not as if someone designed it in
{{TARGET_LANG}}.

**Motivation.** {{MOTIVATION}}
<!-- Name the class of problem the target eliminates, or the concrete constraint driving
     this. "We prefer {{TARGET_LANG}}" is not a motivation and predicts a port that
     stalls halfway. -->

**Non-goals.** No architecture redesign. No new features. No bug fixes (R3). No
idiomatic cleanup until after cutover.

**Definition of done.** {{DONE_CRITERIA}}
<!-- e.g. "The ported suite is green, and the differential harness in §7 shows byte-equal
     responses for all N recorded request/response pairs." -->

---

## 2. Preconditions

Check each honestly before starting. A failed precondition is fixed *before* the port,
not during.

| # | Precondition | Status | Note |
|---|---|---|---|
| 1 | A test suite that defines "correct" | | The suite is the specification. Weakest link in most ports. |
| 2 | The suite runs green on the source **today** | | Establish the baseline before changing anything. |
| 3 | A type checker / compiler in the target | | Its error list becomes the work queue. Dynamic targets roughly double the validation burden — see §7. |
| 4 | Feature freeze on the source, or a rebase strategy | | Otherwise the target moves under you. |
| 5 | A single owner with authority to approve and to stop | | |
| 6 | Reproducible environment for both trees | | Same DB, same fixtures, same clock/timezone. |

If precondition 1 or 2 fails, the first wave of `TASKS.md` is **build the harness**, not
translate anything. Say so explicitly rather than proceeding and hoping.

---

## 3. Target stack

Frozen. Adding anything here requires the owner's sign-off (R6).

| Concern | Choice | Version | Replaces (source) | Why |
|---|---|---|---|---|
| Language / runtime | {{TARGET_LANG}} | | | |
| Web framework | | | | |
| DB driver / ORM | | | | |
| Test runner | | | | |
| | | | | |

**Explicitly rejected**, so the question does not get reopened per-file:

| Rejected | Reason |
|---|---|
| | |

---

## 4. Layout mapping

How the source tree becomes the target tree. Concrete, not schematic.

| Source | Target | Note |
|---|---|---|
| `{{SOURCE_DIR}}/models/` | `{{TARGET_DIR}}/…/models/` | one file → one file |
| | | |

Anything in the source that has **no** target counterpart, and why:

| Source path | Disposition | Why |
|---|---|---|
| | keep as-is / port later / out of scope / replaced by a dependency | |

---

## 5. Module inventory

One row per source file. This is both the risk map and the input to `TASKS.md` §1.
Sort by dependency depth — leaves first.

| Source file | LOC | Depth | Has tests | Risk | Target file | Wave |
|---|---|---|---|---|---|---|
| | | | | low/med/**high** | | |

Mark **high risk** when any of these hold: no test coverage · heavy use of a construct
the divergence audit flagged · the largest files in the tree · anything touching
money, dates, auth, or a regulatory output. High-risk modules still receive exactly one
fresh-context reviewer, like every other module.

---

## 6. Divergence pre-audit

Worked through `references/divergence-catalog.md`, category by category. The findings
live in `PORTING.md` §6; this section records **which categories were audited** so a
reader knows what was checked versus never considered.

| Category | Audited | Rows produced | Note |
|---|---|---|---|
| | yes / no / n-a | | |

An honest `no` is more useful than an unexamined `yes`.

---

## 7. Validation harness

How correctness gets proven. Build this before bulk translation, not after.

1. **Source baseline.** {{HOW_TO_RUN_SOURCE_SUITE}} — recorded green on {{DATE}}.
2. **Differential harness.** {{DIFFERENTIAL_APPROACH}}
   <!-- The highest-value item here. Capture real source behavior as fixtures — recorded
        request/response pairs, golden files, DB snapshots — and assert the target
        reproduces them byte for byte. Without this, "it passes" only means the ported
        tests pass, which is circular when the tests were ported by the same process. -->
3. **Ported suite.** {{HOW_TO_RUN_TARGET_SUITE}}
4. **Skip audit.** Compare test counts, number for number, against the source suite.
   A port that "passes" because tests stopped being collected is the most expensive
   failure available — it is discovered by users.

---

## 8. Waves and gates

Do not start wave *n+1* until wave *n*'s gate is met. Order follows dependency depth
(leaves first); where areas sit at the same depth, order by the owner's priority from
Phase 1 Step 3 (question 18). Wave 0's files come from Step 3 question 19.

**Priority order (owner, Step 3 Q18):** {{PRIORITY_ORDER}}

| Wave | Content | Gate |
|---|---|---|
| 0 | Trial run: 2-3 representative files, hand-driven | The files are correct **and** every ambiguity is folded back into `PORTING.md`. Expect to rewrite parts of it — that is this wave's deliverable. |
| 1 | Scaffold: skeleton, build, CI, deps, harness | The target builds and the empty suite runs. |
| 2 | | |
| … | | |
| n | Cutover | Full suite green · skip audit passed · differential harness clean · owner's manual pass. |

---

## 9. Risks

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| | | | |

Always consider: the largest file in the tree · anything with no test coverage · any
output a human eyeballs rather than asserts (PDFs, emails, reports) · anything the
source embeds at build time that the target must load at runtime · anything reading a
CWD-relative path.

---

## 10. Open decisions

Decisions deferred past Phase 1. Each blocks a specific wave — say which.

| # | Decision | Blocks | Owner | Due |
|---|---|---|---|---|
| | | | | |
