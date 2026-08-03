# Phase 1 — Setup

Run once per port. Produces four files. **Translate nothing in this phase** — not one
file, not "just the trivial ones to get moving". The deliverable is the rulebook that
every later session depends on; a rule you discover after translating 40 files costs 40
re-reviews.

**Gate:** `PORT-PLAN.md`, `PORTING.md`, `TASKS.md`, and `FINDINGS.md` all exist in the
target directory, the owner has read `PORT-PLAN.md` §3 and `PORTING.md`, and `TASKS.md`
has a populated Wave 0.

---

## Step 1 — Collect the three inputs

Required before anything else. **If any one is missing, stop and ask — then end the turn.**
Step 2 does not begin until all three are confirmed.

| Input | Example | If missing |
|---|---|---|
| Source directory | `backend/` | Ask. Do not infer from the repo root, and not from the only plausible candidate either. |
| Target directory | `backend-py/` | Ask. Confirm before creating it if it already exists and is non-empty. |
| Target language / stack | `Python` | Ask. "Python" is enough to start; the framework comes out of the interview. |

Ask for all the missing ones in a single batch. Offer candidates only from what the owner
has already told you — proposing directories means you went looking, and looking is Step 2.

Then confirm back the three values in one line and continue. Do not ask the interview
questions yet — you cannot ask good ones before reading the code.

---

## Step 2 — Analyze the source

Read broadly before deciding anything. Fan this out across parallel subagents when the
tree is large; one agent per area, each reporting a structured summary rather than file
contents.

Produce, in this order:

1. **Size and shape.** File count, LOC, LOC excluding tests, and a per-area breakdown.
   Name the three largest files explicitly — they dominate the schedule and the risk.
   **Write this into `PORT-PLAN.md` §0, including the commands you counted with.** Count
   the tree; do not estimate it, and do not carry the numbers only in your reply — §0 is
   the baseline every later progress claim is measured against, and it is re-measured at
   cutover. State what the count excludes (generated files, vendored deps, fixtures).
2. **Dependency depth.** Which modules import which. This produces the wave ordering:
   leaves first. Step 3 asks the owner for a priority order *within* that constraint —
   record the answer against `PORT-PLAN.md` §8.
3. **Test coverage reality.** Which areas have tests, which have none, and whether the
   suite currently runs green. Run it if you can. **A port with no green baseline has no
   definition of correct** — say so plainly rather than proceeding quietly.
4. **External surface.** Every boundary that outlives the port: HTTP routes, CLI commands,
   queue topics, DB schema, file formats, emitted documents. This becomes the acceptance
   checklist. Enumerate it exhaustively; a missed route is a missed regression.
5. **Third-party dependencies.** Each one, and whether the target ecosystem has an
   equivalent. Flag the ones that do not — they are the highest-risk items in the plan.
6. **Divergence pre-audit.** Work `references/divergence-catalog.md` category by category
   against this specific pair. Do not freehand it.

Record what you *did not* read, in `PORT-PLAN.md` §0 under **Not read**, with the file
and LOC counts for those paths. An honest gap in the inventory is useful; a silent one is
a landmine.

---

## Step 3 — Draft the plan, then interview

Write `PORT-PLAN.md` from the template with everything Step 2 established, leaving the
interview-dependent fields as `{{PLACEHOLDER}}`. Then ask.

**Ask in one batch, not one at a time.** Group them, propose a default for each with a
one-line reason, and let the owner correct rather than compose. Skip any question the
source or the target ecosystem already answers unambiguously — and say which ones you
skipped and what you assumed.

### The question set

Cover every group. A group you skip gets decided later, silently, differently in each
file — that is exactly what happens without this step.

**A. Scope and motivation**
1. What class of problem does the target eliminate? (Drives every later trade-off.)
2. Replacement or parallel implementation? If clients must keep working unchanged, the
   external contract is frozen and that constrains everything downstream.
3. Anything explicitly out of scope, left in the source language, or deleted rather than
   ported?
4. Is the source frozen for the duration? If not, what is the rebase plan?

**B. Stack** — propose a concrete default for each, with the reason.
5. Framework / runtime.
6. Data access: the near-verbatim path or the ecosystem's idiomatic one? **Recommend the
   near-verbatim path** and get explicit sign-off, because it produces deliberately
   un-idiomatic target code and the owner should not be surprised by that later.
7. Test runner, and whether the existing suite is ported or rewritten.
8. Anything to *reject* explicitly, so the question is not reopened per-file.

**C. Fidelity policy** — the questions whose absence causes the most damage.
9. Naming: preserve source names verbatim, or convert to target convention? If convert,
   the exact casing rule, including acronyms.
10. Error idiom: how the source's errors become the target's, and what happens to callers
    that ignored them.
11. Null / absent / zero-value policy at the serialization boundary.
12. Escape hatches: which are permitted, and are they counted?
13. Stub policy confirmation: loud failure plus an open question — never a guess.

**D. Process**
14. Who is the owner — the one who approves rule changes and says "stop"?
15. Version control: does the porting agent commit, or is that the operator's? Be explicit;
    ambiguity here has destroyed peers' work in real ports.
16. Review: one fresh-context reviewer per unit. Are there any constraints on reviewer
    access or turnaround time?
17. What does "done" mean for the whole port?

**E. Ordering and starting point**
18. Dependency depth fixes wave order at the boundary — leaves first — but is there a
    priority order *within* that? Name any area you need working sooner (a demo, a
    deadline, a stakeholder ask) and any area to defer.
19. Wave 0 candidates: the port will otherwise propose 2-3 representative files for the
    trial run from the Step 2 analysis. Do you have specific folders or files you want it
    to start with, or any to avoid?

**F. Anything the analysis surfaced** — the specific ambiguities Step 2 found. These are
usually the most valuable questions in the batch, and they are different every time.

Record every answer in `PORT-PLAN.md` or as a `PORTING.md` rule. An answer that lives only
in the conversation is lost at the end of the session — this is the single most common way
a port loses its rules.

---

## Step 4 — Write the four documents

From `templates/`. Copy them into the target directory and fill them in.

| File | Must contain before the gate passes |
|---|---|
| `PORT-PLAN.md` | §0–§9 complete, §0 counted from the tree rather than estimated. No `{{PLACEHOLDER}}` left except in §10. |
| `PORTING.md` | R1–R10 verbatim from the template. Every other section either filled or explicitly deleted as not-applicable. §6 populated from the pre-audit. |
| `TASKS.md` | Wave 0 and Wave 1 populated with real rows. Later waves may be outlines. |
| `FINDINGS.md` | All four section headers, plus every question Step 3 could not resolve. |

Two rules about the rulebook itself:

- **Every rule must be checkable.** "Handle errors carefully" is not a rule. "Where the
  source logs and continues, the target logs and continues" is. If a reviewer cannot cite
  it against a specific line, rewrite it.
- **Every rule needs a real example from this codebase.** A rule illustrated with a
  generic snippet gets interpreted differently by every agent that reads it.

---

## Step 5 — Trial run (Wave 0)

If the owner named starting files or folders in Step 3 (question 19), pick 2-3 files from
there; add a risky or tested file from the Step 2 analysis only if their list doesn't
already cover one. Otherwise pick 2-3 files that are *representative*, not easy: one
typical module, one that touches the riskiest area, one with tests — and confirm the pick
with the owner before porting anything, since Wave 0 sets the pattern every later wave
follows. Port them with the full Phase 2 loop.

**The deliverable of this wave is an amended `PORTING.md`, not the three files.** Expect
to add, rewrite, and contradict rules. Every ambiguity encountered gets folded back in as
a numbered rule before Wave 1 starts.

Then freeze `PORTING.md`. After the freeze, changing a rule requires the owner's sign-off
and an `amend` entry in the ledger, because everything translated before the change
followed the old text.

---

## Step 6 — Report

State: the four files and where they are · size/shape of the source, quoting the §0
totals (files, LOC, LOC excluding tests) and the three largest files · the stack chosen ·
how many divergence rows the pre-audit produced and which categories came back `n-a` ·
the open questions still blocking · what Wave 0 contains and what it changed in
`PORTING.md` · what you did not read.

Then stop. The owner approves the plan before Wave 1 begins.
