# Phase 2 — The port loop

One unit of work, end to end. You are the **implementer**. You never review your own
work — a separate agent with its own context does that, and the context separation *is*
the mechanism.

Repeat this loop per task. Do not batch several tasks and review once at the end.

---

## 0 — Claim

Read `PORTING.md` in full. Read `TASKS.md` §1 and take the **lowest-ID available row**
whose dependencies are all `DONE`, unless the operator named a specific task.

If its dependencies are not ported yet, say which ones are missing and ask — do not port
against a target that does not exist.

Set the row to `DOING`. One task at a time.

---

## 1 — Orient

- Read the source file **in full**. Not the parts you plan to change — all of it.
- Read the mapped target path if it exists.
- Read the `PORTING.md` sections that apply, and every §6 divergence row that touches
  this module.
- Check `FINDINGS.md` for open questions and source defects already filed against this
  file.

Then state, in three lines: target path · LOC · which divergence rows apply · whether
the scope is partial.

---

## 2 — Translate

Write the target file. Mechanically. The output should diff meaningfully against the
source, function for function, in the same order.

While you write, the only three legitimate responses to a difficulty are:

| Situation | Response |
|---|---|
| The target has an equivalent construct | Use it. Same shape, same position. |
| The target has **no** equivalent | Stub it: correct signature, loud failure carrying `TODO(port): <reason>`, and an open question in `FINDINGS.md` (R5). |
| The source is wrong | Port the bug faithfully. Add a source defect to `FINDINGS.md` (R3). |

There is no fourth response. "Invent something reasonable" is not on this list — that is
the single most common way a mechanical port stops being verifiable.

Port the module's tests in the same unit of work, at the mapped test path (R37).

### Stop conditions

Any of these means you are no longer doing a mechanical port. Stop, revert that edit, and
take one of the three responses above.

- You are writing a function, class, method, or parameter the source does not have.
- You are deleting a statement because it looks like debug residue or looks redundant.
- You are making a type narrower, stricter, or more precise than the source's.
- You are renaming a field, or changing a name in a way `PORTING.md` §1 does not authorize.
- You are changing whitespace inside an embedded literal — SQL, regex, format string,
  template.
- You are collapsing two similar branches into one.
- You are removing a check because the target "can't fail there".
- You are about to write a comment explaining why a difference is acceptable.

---

## 3 — Adversarial review

Launch **exactly one fresh-context subagent** with `references/reviewer-prompt.md` for
every module, including any module marked high risk in `PORT-PLAN.md` §5.

The prompt contains **only**: the target path, the source path, the path to `PORTING.md`,
which sections apply, and the relevant §6 row numbers.

**Never include** your rationale, your uncertainty, what you found hard, what you decided
and why, or any hint about where you think the bugs are. Leaking your reasoning re-creates
your blind spots inside the reviewer and destroys the value of the pass. A review that
inherits your context yields nothing and costs the same.

---

## 4 — Fix

Apply the findings that are correct.

For each finding you **reject**, state the concrete reason with a `file:line` citation
into the source. "I disagree" and "that's intentional" are not reasons. If you cannot cite
a line, the finding stands.

Do not fix anything the reviewer did not raise. Do not redesign in response to a finding —
a finding about one line is fixed on that line.

---

## 5 — Test

In order, and do not skip a rung:

1. The ported tests for this module.
2. The differential harness for anything this module newly enables (`PORT-PLAN.md` §7).
3. The full suite of everything ported so far — no regressions.

A test assertion that has to change is classified in `FINDINGS.md` §4 as **port defect**
or **source implementation detail** (R38), with the detail named. Never leave one
unclassified. Never delete or skip a test to make the wave green (R39).

If you cannot run a rung, say which and why. Do not report a rung you did not run.

---

## 6 — Update TASKS.md

**Before reporting back, not after.** In the same session that did the work.

Four ledger entries in §2, kept separate:

```
[T12] port      <target> ← <source> (<LOC>, <scope note>)
[T12] review    <target> — N findings: <one clause each, citing rule IDs>
[T12] fix       findings <applied> applied; <rejected> rejected — <reason + source:line>
[T12] test      <suite> N/N · full suite M/M
[T12] DONE      <new stubs> · FINDINGS: <new rows>
```

Then set the §1 row to `DONE` — only if the tests are green and every finding is applied
or rejected in writing. Otherwise `BLOCKED`, with the blocker named in Notes.

Update §3 metrics if the wave ended.

---

## 7 — Report

Close with exactly these, in order:

1. **Target path and LOC.**
2. **Deviations from the source.** Every place the target does something the source did
   not, however small — including anything you simplified, made safer, made more
   idiomatic, tightened, or dropped. For each: what the source does, what the target
   does, and the `PORTING.md` rule that authorizes it. **A deviation with no rule
   authorizing it is a defect** — go back to step 2 and revert it.
3. **Findings** raised / applied / rejected.
4. **Tests**: which rungs ran and their results.
5. **New rows** in `FINDINGS.md`, by ID.
6. **New `TODO(port)` stubs**, by location.
7. **Task status** and whether the wave is green.

Item 2 is not optional and not a formality. In baseline testing, an agent producing a
"clean" port had made seventeen unrequested changes — deleted error logging, invented
methods, narrowed a type in a way that would break on data its own source tests created —
and reported none of them until an exhaustive deviation list was explicitly demanded. The
deviations are invisible unless you enumerate them.

Then stop. The operator decides whether to continue to the next task.
