---
name: porting-codebases
description: Use when porting, translating, migrating, or rewriting an existing working codebase from one programming language or stack to another — Go to Node, Python to Rust, Java to Kotlin, PHP to TypeScript. Covers both starting a port (analyzing the source, choosing the target stack, writing the rules) and executing one unit of translation. Trigger phrases include "port this to X", "rewrite the backend in X", "translate this codebase", "portear a X", "migrate from X to Y", and requests to continue or resume an in-progress port.
argument-hint: "<port dir> [T12|continue|resume]  |  <source dir> <target dir> <target language>"
---

# Porting Codebases

A **mechanical port**: translating a working codebase from language `S` to language `T`
while preserving architecture, module boundaries, names, and control flow. The result
should read as if the source were transpiled — not as if someone designed it in `T`.

## Step 0 — Arguments gate

**Invoked with no arguments → stop and ask. Do nothing else first.**

Routing itself depends on the arguments: you cannot answer "does `PORTING.md` exist in the
target directory" without being told the target directory. So this gate comes before
everything — before reading the source, before `ls`, before proposing options.

| Invocation | Action |
|---|---|
| No arguments | **Stop.** Ask for the three inputs in one batch. Wait for the answer. |
| `<port-directory>` | Treat it as an existing port and run the **port-directory gate** below. Never initialize from this form. |
| `<port-directory> <T12\|continue\|resume>` | Run the **port-directory gate**, then resolve the requested work in that directory's `TASKS.md` → Phase 2. |
| `<source> <target> <language>` | Confirm the three back in one line → route below. |
| A task id (`T12`, `continue`, `resume`) | Resolve it against `TASKS.md` → Phase 2. If no `TASKS.md` is reachable, treat as no arguments and stop. |
| Other partial input (e.g. "port the backend to Rust" with no target dir) | **Stop.** Ask only for the missing values; do not infer the rest. |

The three inputs are **source directory**, **target directory**, **target language/stack**.
Ask for them as plain sentences, not field labels — e.g. "Where does the current code
live, where should the translated version go, and what language or framework should it be
written in?" The labels above are for you to track internally; the owner should never have
to parse them.

Stopping means: ask, then end the turn. Not "ask and start analyzing while waiting." A
repo survey run before the source directory is confirmed analyzes the wrong tree, and its
output biases every option you go on to propose.

Never infer a missing value from the repo root, from a single obvious candidate directory,
or from what the tree "clearly" is. A repo with exactly one backend is not consent to port
that backend. Guessing the target directory is worse still — it decides where dozens of
files land, and the owner discovers it after they exist.

### Port-directory gate

Use this gate whenever the invocation supplies a port directory, even when it also names
a task.

1. Check the exact supplied path. Do not search parent, child, or sibling directories.
2. If it is not an existing directory, **stop** and report that the port cannot be resumed.
   Ask for the correct port directory, or for all three inputs if the owner intends a new
   port. Do not create the directory and do not start Phase 1.
3. Check that these four required state files are readable regular files in that directory:
   `PORT-PLAN.md`, `PORTING.md`, `TASKS.md`, and `FINDINGS.md`.
4. If any are missing or not regular readable files, **stop** and list each invalid path.
   Do not regenerate or overwrite state and do not fall back to Phase 1.
5. If all four pass, read them from that directory, recover the source directory and target
   stack from `PORT-PLAN.md` / `PORTING.md`, and continue in Phase 2. For `continue` or
   `resume`, claim the next eligible task from this directory's `TASKS.md`; for `T12`, claim
   that row.

The one-directory form means **resume this port**, not "this is probably the source" and
not "start a fresh port here". Phase 1 is entered only from the explicit
`<source> <target> <language>` form when the target has no rulebook yet.

---

For the explicit three-input form, route by whether the complete port state exists yet.

```
Do all four required state files exist in the target directory?
├── no  → Phase 1: references/phase-1-setup.md
│         Analyze, interview, write PORT-PLAN.md + PORTING.md + TASKS.md + FINDINGS.md.
│         Translate nothing.
└── yes → Phase 2: references/phase-2-port-loop.md
          One task: orient → translate → adversarial review → fix → test → update TASKS.md.
```

If explicitly given `<source> <target> <language>` and any required state file is missing,
you are in Phase 1 — even if the request was "just port this one file". This fallback does
not apply to the one-directory resume form, which must stop on incomplete state. Nothing
gets translated without the complete port state.

## The four documents

Every port maintains exactly these, in the target directory. They are the port's memory:
a decision that lives only in a conversation is gone at the end of the session.

| File | Role |
|---|---|
| `PORT-PLAN.md` | Scope, stack, inventory, waves, gates, risks. Written once, amended by sign-off. |
| `PORTING.md` | Numbered rules `R1`…`Rn`. Reviews cite them instead of trading opinions. |
| `TASKS.md` | The current work queue plus an append-only status history. **Updated whenever a task is added or changes status, and before reporting back.** |
| `FINDINGS.md` | Open questions · source defects · divergences · test deltas. Written at the moment of doubt. |

Templates in `templates/`. Copy and fill; do not improvise the structure.

### TASKS.md history

Treat the queue and history as different views of the same work:

- The queue row stores the task's **current** status. Change it in place from `TODO` to
  `DOING`, then to `DONE` or `BLOCKED`.
- The ledger is **append-only** and stores every status transition. When a task is created,
  append `[T12] TODO <scope>`. When it is claimed, append `[T12] DOING <scope>`. Finish
  with the existing `port`, `review`, `fix`, and `test` entries followed by `[T12] DONE ...`,
  or append `[T12] BLOCKED <reason>`.
- Never delete completed queue rows, remove ledger entries, reuse a task ID, or collapse
  old `TODO`, `DOING`, `BLOCKED`, and `DONE` events into the current row.
- When a blocked task is reopened, append a new `TODO` event and then a new `DOING` event;
  preserve the earlier `BLOCKED` event.
- For an existing `TASKS.md` that lacks old transition events, do not fabricate them.
  Preserve what exists and record every transition from the current session onward.

## Hard rules

These hold in both phases and override your judgment about good code.

1. **Translate, do not redesign.** Preserve structure, order, names, control flow, and
   embedded literals verbatim. Idiomatic `T` comes after the port ships.
2. **Never fix a source bug.** Record it in `FINDINGS.md` and reproduce it faithfully. A
   port that changes behavior cannot be validated against the original.
3. **Never guess.** No equivalent in `T` → stub that fails loudly with `TODO(port):` plus
   an open question. Never invent a plausible implementation.
4. **The reviewer is a separate agent with a fresh context.** You never review your own
   port. Never leak your reasoning into the reviewer's prompt.
5. **Preserve task history.** Never delete a task or its ledger events. Record every new
   task and every status transition in `TASKS.md` when it happens.
6. **Every deviation is reported.** Enumerate them at the end of every task, each with the
   rule that authorizes it. A deviation with no rule is a defect.
7. **The test suite is the specification.** A test that must change is a finding, never a
   chore. Classify it. Never delete or skip one to make a wave green.
8. **Escape hatches are expected.** `any`, `unsafe`, raw casts — use them rather than
   redesigning. Count them; burn them down after cutover.

## Rationalizations

From baseline runs of this exact task. An agent porting a 122-line file with no rulebook
made seventeen unrequested changes and reported none until an exhaustive deviation list
was demanded. Every phrase below is verbatim from that transcript.

| Rationalization | Reality |
|---|---|
| "This is the idiomatic equivalent" | Idiomatic is the thing you were told not to do. The port is deliberately non-idiomatic. |
| "There's no equivalent, so I created one" | No equivalent means **stub plus open question**, not invent. This is the highest-damage loophole. |
| "It's a debug leftover with no logger" | It ran in production. Removing it changes behavior you cannot see. Port it. |
| "There's nothing to check here" | The source checked it. Port the check, even if it can never fire. |
| "This class of bug is gone rather than ported" | Then the target cannot be validated against the source. Port the bug; file it under source defects. |
| "It throws a clear error instead of crashing" | A crash and a typed error are different observable behaviors. |
| "Semantically identical" — of reflowed literals | Something downstream hashes, diffs, or matches on that text. Byte-for-byte. |
| "Stricter than the source" | Narrowing a type rejects inputs the source accepted. Production data does not care that the narrow type is nicer. |
| "The caller contract is cleaner this way" | Every call site changes. That is a redesign wearing a port's clothes. |
| "I'll note it in the report instead" | A noted deviation is still a deviation. Revert it, or find the rule that authorizes it. |
| "It's just naming" | Names are the diff surface. A reviewer reads both files in parallel. |
| "Only a small cleanup" | Seventeen small cleanups is how a port becomes unverifiable. |
| "There's only one backend, so that's obviously the source" | One candidate is not consent. Stop and ask. |
| "I'll survey the repo first so I can propose better options" | The survey *is* the inference. Ask first, read after. |
| "I'll assume the target dir and rename it later if wrong" | It decides where dozens of files land. The owner finds out once they exist. |

## Red flags — stop and revert

- Reading, listing, or sizing up the source tree before the three inputs are confirmed
- Proposing a source or target directory the owner never named
- Writing a function, method, parameter, or export the source does not have
- Deleting a statement that looks like debug residue
- Making a type narrower or stricter than the source's
- Removing a check because the target "can't fail there"
- Changing whitespace inside SQL, a regex, a format string, or a template
- Collapsing two similar branches
- Writing a comment that explains why a difference is acceptable
- Reporting a task done without updating `TASKS.md`
- Adding, claiming, blocking, reopening, or completing a task without appending its status
  event to the `TASKS.md` ledger
- Deleting a completed task row or rewriting ledger history to show only the latest state
- Reviewing your own port, or telling the reviewer what you were unsure about

**All of these mean: revert the edit, then stub it, port it faithfully, or file it.**

## References

| File | Read when |
|---|---|
| `references/phase-1-setup.md` | Starting a port. Analysis, the interview question set, the four documents. |
| `references/phase-2-port-loop.md` | Executing one task. |
| `references/divergence-catalog.md` | Phase 1 pre-audit, and whenever a review turns up a semantic surprise. |
| `references/reviewer-prompt.md` | Step 3 of every task. Send verbatim. |
