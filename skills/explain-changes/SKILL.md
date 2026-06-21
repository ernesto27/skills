---
name: explain-changes
description: Explains code and config changes in detail with terminal diagrams, for any domain — backend, frontend, infra, data, mobile. Walks through what changed, why it matters, and how data/control flows through the modified code, rendering architecture, sequence, and before/after diagrams as ASCII art directly in the terminal. Use when the user wants to understand a diff, a commit, a PR, a file, or a feature they (or someone else) just wrote.
when_to_use: User asks to "explain this change", "what does this diff do", "walk me through this PR/commit", "explain these changes", "show me a diagram of this", "explain this file/function", "what changed and why". Trigger phrases include "explain change", "explain diff", "explain commit", "explain PR", "diagram this", "walk me through", "what does this do".
---

# Explain Changes — Detailed Walkthrough with Terminal Diagrams

You explain code changes thoroughly and render diagrams as ASCII/box-drawing art so they
display correctly in any terminal. Your goal: the reader finishes with a precise mental
model of *what* changed, *why*, and *how the code now behaves* — not just a list of edited lines.

## When to use

- "Explain this change / diff / commit / PR"
- "Walk me through what I just wrote"
- "Diagram how this works"
- "Explain this file / function / feature"

## Step 1 — Determine the scope of "the change"

Figure out exactly what to explain, in this order of preference:

1. **Explicit target** the user named (a file, function, commit hash, PR number, branch).
2. **Uncommitted work** — run `git status` and `git diff` (staged + unstaged):
   ```bash
   git status
   git diff            # unstaged
   git diff --staged   # staged
   ```
3. **A specific commit / range**:
   ```bash
   git show <hash>
   git diff <base>..<head>
   ```
4. **A PR / branch** — diff the branch against its base with git:
   ```bash
   git fetch                          # make sure refs are current
   git log --oneline <base>..<branch> # commits on the branch
   git diff <base>...<branch>         # changes the branch introduces
   ```

If the scope is ambiguous (e.g. there are both staged and unstaged changes, or multiple
recent commits), state what you're explaining in one line before diving in. Only ask the
user when genuinely undecidable.

## Step 2 — Read enough context to explain *why*, not just *what*

A diff shows the *what*. To explain the *why* and the *how*, read the surrounding code:

- Open the changed files and read the code/config around each hunk.
- Trace what calls into and out of the changed unit so you can describe ripple effects.
- Check for new/changed signatures, interfaces, routes, schemas, configs, infra resources,
  UI components, or external calls — whatever the change touches.
- Note anything risky for that domain, for example:
  - **Backend**: error handling, concurrency, nil/empty cases, breaking API changes.
  - **Frontend**: state/rendering, accessibility, broken props/contracts, loading/error states.
  - **Infra/config**: irreversible or destructive ops, secrets, blast radius, rollback path.
  - **Data/migrations**: changes that can't be rolled back, data loss, long locks.

Do not narrate your file-reading. Read silently, then explain.

## Step 3 — Write the explanation

Structure the response like this (omit sections that don't apply):

### 1. TL;DR
2–4 sentences: what changed and the user-visible / behavioral effect.

### 2. Change inventory
A compact table of touched files and the role each plays:

```
┌─────────────────────────────┬──────────┬────────────────────────────────────────┐
│ File                        │ Change   │ Purpose                                  │
├─────────────────────────────┼──────────┼────────────────────────────────────────┤
│ src/handlers/profile.*      │ +new     │ Entry point for the new feature          │
│ src/services/profile.*      │ +new     │ Business logic / data access             │
│ src/routes.*                │ modified │ Wires the new route                      │
│ db/migrations/..._profile.* │ +new     │ Adds the backing table                   │
└─────────────────────────────┴──────────┴────────────────────────────────────────┘
```

### 3. Detailed walkthrough
Walk through change-by-change (group by logical unit, not by line). For each:
- What it does, in plain language.
- Why it's needed / what problem it solves.
- Any subtlety: edge cases, error paths, assumptions, performance, security.
- Reference code as `file_path:line` so the reader can click through.

### 4. Diagrams (ASCII)
Always include at least one diagram. Pick the type(s) that fit the change — see the diagram
catalog below. Diagrams must be pure ASCII / Unicode box-drawing inside a fenced code block
so they render in the terminal. Never use Mermaid, PlantUML, or image links — those don't
render in a terminal.

### 5. Risks & follow-ups
Bullet list: bugs you suspect, missing tests, breaking changes, TODOs, things to verify.
If you found nothing concerning, say so in one line.

## Diagram catalog (ASCII templates)

These are starting points, not a closed set. Choose (or invent) whatever fits the change —
e.g. a component tree for a frontend change, a topology/network diagram for infra, a pipeline
for data flow. Keep diagrams narrow (≤ ~78 cols) so they don't wrap in a terminal. Prefer
Unicode box-drawing (`│ ─ ┌ ┐ └ ┘ ├ ┤ ┬ ┴ ┼ → ←`) over `+--+`, but stay consistent within a
diagram.

### A. Request / sequence flow — for request handling, auth, middleware, message passing
```
Caller            Router          Middleware        Handler           Backend / Store
  │                  │                 │                 │                 │
  │  request         │                 │                 │                 │
  │ ───────────────► │                 │                 │                 │
  │                  │ authenticate    │                 │                 │
  │                  │ ──────────────► │                 │                 │
  │                  │                 │ attach identity │                 │
  │                  │                 │ ──────────────► │                 │
  │                  │                 │                 │ create(record)  │
  │                  │                 │                 │ ──────────────► │
  │                  │                 │                 │ ◄──── result ───│
  │ ◄──── response ──────────────────────────────────────────────────────│
```

### B. Before / after — for refactors and behavior changes
```
        BEFORE                              AFTER
┌──────────────────────┐          ┌──────────────────────────┐
│ handler reads input  │          │ handler reads input       │
│ → talks to store     │   ──►    │ → validates input         │
│   directly           │          │ → delegates to service    │
│ → returns raw data   │          │ → returns shaped response │
└──────────────────────┘          └──────────────────────────┘
  no validation, leaks            validated, layered, safe
```

### C. Component / dependency graph — for wiring, dependency injection, new modules
```
        entry point
          │ builds
          ▼
   ┌─────────────────┐      injects     ┌──────────────────┐
   │ dependencies    │ ───────────────► │ Handler          │
   │  store, auth,   │                  │  uses Service    │
   │  logger         │                  │                  │
   └─────────────────┘                  └────────┬─────────┘
                                                  │ queries
                                                  ▼
                                          ┌──────────────┐
                                          │ data store   │
                                          └──────────────┘
```

### D. State machine — for status fields, lifecycle changes
```
   ┌─────────┐   approve   ┌──────────┐   suspend   ┌───────────┐
   │ pending │ ──────────► │ approved │ ──────────► │ suspended │
   └─────────┘             └──────────┘             └───────────┘
        │                       ▲                         │
        │ reject                └───────── reinstate ──────┘
        ▼
   ┌─────────┐
   │ rejected│
   └─────────┘
```

### E. Data shape / schema diff — for storage, model, or contract changes
```
record  (NEW)
┌────────────────┬───────────────┬───────────────────────────┐
│ field          │ type          │ notes                     │
├────────────────┼───────────────┼───────────────────────────┤
│ id             │ identifier PK │                           │
│ owner_id       │ reference FK  │ → owner(id)               │
│ status         │ enum          │ pending|approved|...      │
│ created_at     │ timestamp     │ set on creation           │
└────────────────┴───────────────┴───────────────────────────┘
```

## Style rules

- **Always render at least one diagram**, in a fenced code block, pure ASCII/Unicode.
- Match the diagram type to the change; use multiple if it genuinely helps (e.g. a sequence
  diagram + a schema diff for a new endpoint backed by a new table).
- Keep diagrams under ~78 columns wide; align boxes; double-check the art lines up.
- Explain *why*, not just *what*. The reader can read the diff themselves.
- Use `file_path:line` references so paths are clickable.
- Be precise about behavior changes, error paths, and anything that could break.
- Don't invent behavior — if you're unsure how something flows, read the code or say so.
- Stay domain-agnostic. This skill works for any kind of change — backend, frontend, infra,
  data, mobile, config, docs. Adapt the diagrams and vocabulary to whatever the change is
  about, and match the terminology the project already uses (read CLAUDE.md / README / the
  surrounding code) instead of imposing a fixed stack.
