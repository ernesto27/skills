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

**Phrase every question in plain language.** Say what the concrete choice is and what it
changes *before* you name it technically, and spell out any acronym or term of art the
first time it comes up. The owner should be able to answer without already knowing porting
vocabulary — precision is not the same thing as jargon. "Data access: near-verbatim or
idiomatic?" asks the right thing but only you can parse the shorthand; "Should the new code
copy the source's exact database queries, or rewrite them using the new language's standard
tools?" asks the same question as a sentence anyone on the team can answer. The list below
is written the way you should actually ask it — use it as the wording, not just the
checklist.

### The question set

Cover every group. A group you skip gets decided later, silently, differently in each
file — that is exactly what happens without this step.

**A. Scope and motivation**
1. What's driving this port — what problem with the current codebase are we trying to fix
   (slow performance, an unmaintained framework, hard to hire for, licensing)? This shapes
   every trade-off that follows.
2. Will the new version replace the old one outright, or run alongside it for a while? If
   other services or apps calling into this one can't change, then every URL, request and
   response shape, and file format it exposes is frozen — that constrains everything
   downstream.
3. Is there anything we should deliberately *not* port — leave running in the old language,
   or just delete?
4. Will the old codebase stop changing while we work on the port, or will people keep
   shipping fixes and features to it in parallel? If it keeps moving, how do we periodically
   pull those changes into the port so we're not translating a moving target?

**B. Stack** — propose a concrete default for each, with the reason.
5. Which framework and runtime should the new code run on?
6. How should the new code talk to the database — copy the source's exact queries and
   data-access pattern, or rewrite it using the new ecosystem's normal tools (an ORM, a
   query builder)? **We recommend copying it as-is**, and want explicit sign-off on that:
   it's the only way to know the port behaves identically, but it means the new code won't
   look like something written fresh in the target language, and the owner shouldn't be
   surprised by that later.
7. Which test runner should we use, and should the existing tests be translated line-for-line
   or rewritten from scratch?
8. Anything you want to rule out up front — a library, a pattern, an approach — so we're not
   re-deciding it on every file?

**C. Fidelity policy** — the questions whose absence causes the most damage.
9. Should we keep the source's exact names (variables, functions, files), or convert them
   to the new language's naming convention (e.g. `snake_case` to `camelCase`)? If we
   convert, what's the exact rule, including how acronyms like "ID" or "URL" get cased?
10. How should the source's error-handling style map onto the new language (for example,
    a language that returns error values becoming one that throws exceptions, or the
    reverse)? And where the old code silently ignored an error, should the new code keep
    ignoring it, or is that worth flagging as a bug?
11. At the boundary where data gets serialized — API responses, files, database rows — how
    should missing, null, or zero values come through? Should an absent field stay absent,
    or become `null`, an empty string, or zero?
12. Which "escape hatches" are allowed when there's no clean equivalent in the target
    language — things like an untyped catch-all value, an unchecked cast, or turning off a
    safety check for one line? Should we keep a running count of how many we use, so we
    know how much cleanup is owed later?
13. Confirming the rule for anything we can't translate: it gets stubbed out with a failure
    that's loud and obvious, plus a written-down open question — never a best-effort guess.
    Does that match what you want?

**D. Process**
14. Who has final say on this port — the person who approves changes to the rules and can
    call a stop if something's off track?
15. Who commits the ported code to git — does the agent commit as it goes, or do you want to
    review and commit it yourself? Please be explicit here: leaving this vague has caused
    real damage on past ports (lost work, agents overwriting each other's commits).
16. Every ported file gets reviewed by a separate reviewer with no memory of how it was
    written. Any constraints we should know about — access limits, how long a review can
    take?
17. How will we know the whole port is finished — full test parity, a specific feature set
    working, sign-off from a particular team?

**E. Ordering and starting point**
18. We'll port in dependency order — modules nothing else depends on go first, so we're
    never building on top of something not yet translated. Within that constraint, is there
    anything you need working sooner (a demo, a deadline, a stakeholder ask), or anything
    that can wait until the end?
19. For the first trial batch (2-3 files), do you have specific files or folders you'd like
    us to start with, or any you'd rather we avoid touching first? If not, we'll pick 2-3
    representative files ourselves from the codebase analysis.

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
