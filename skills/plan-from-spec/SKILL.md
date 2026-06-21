---
name: plan-from-spec
description: Turn a spec/feature definition into a rigorous, reviewed implementation plan saved under plans/. Works for any domain — backend, frontend, infra, database, data, mobile, etc. Interrogates the spec with clarifying questions until nothing is left to assumption, then writes a structured plan document. Use when the user provides a spec or feature description, or asks to "plan", "create a plan from spec", "armar un plan", or "planificar" a piece of work.
when_to_use: User provides a specification, feature brief, ticket, or rough description (for backend, frontend, infrastructure, database, data pipeline, mobile, or any other area) and wants a concrete implementation plan before any work starts. Trigger phrases include "plan from spec", "plan this spec", "crear plan", "armar plan", "planificar", "spec to plan", "design a plan". Also use proactively when a request is large/ambiguous enough that working without a written plan would be risky.
argument-hint: "<spec text / feature description inline> [domain hint: backend|frontend|infra|database|... + extra constraints]"
---

# Plan From Spec

You convert a specification into a **complete, unambiguous implementation plan** and save it under `plans/`. This is **domain-agnostic** — it works for backend, frontend, infrastructure, database, data, mobile, or any mix. Your defining trait: **you do not guess.** Every gap in the spec becomes a question. Nothing reaches the final plan as a silent assumption.

## Input (the args)

The args are **always inline text** — a spec definition plus optional details/constraints, passed directly (a paragraph, bullet list, or feature description). The args may also include a **domain hint** (e.g. "frontend", "infra", "database"). **Never** treat the args as a file path or try to read a spec from a file.

If the args are empty, ask the user to paste the spec inline before doing anything else.

## Step 0: identify the domain(s)

Determine which area(s) the work touches — it may be one or several:
- **Backend** — services, APIs, business logic, jobs.
- **Frontend** — UI, components, state, routing, accessibility.
- **Infra / DevOps** — deployment, CI/CD, networking, scaling, secrets, observability.
- **Database** — schema, migrations, indexes, data integrity, performance.
- **Data** — pipelines, ETL, analytics, schemas, retention.
- **Mobile / other** — platform-specific concerns.

If the domain is unclear from the spec and args, **ask first**. The domain selects which lenses in Step 2 you emphasize.

## Operating principle: leave nothing to chance

A plan is only allowed to be written once **every** relevant item below is either answered by the spec or confirmed by the user. If you find yourself about to write "assume", "probably", "I'll default to", or "TBD" in the plan — stop and ask instead.

## Process (in order)

### 1. Ingest
- Use the inline args text as the spec.
- Read the project's `CLAUDE.md` / `AGENTS.md` / `README` and any obviously relevant existing code, configs, or infra so the plan fits **real conventions of this repo**, whatever the stack. Do not plan against an imagined architecture.

### 2. Build the open-questions list
Scan the spec against the universal checklist, plus the lens(es) matching the domain(s) from Step 0. Record every unknown. Do not skip a category because the spec is short — silence is an unknown, not a default.

**Universal (always):**
- **Scope & boundaries** — what's explicitly in scope, what's out? MVP vs later phases?
- **Actors & permissions** — who uses/triggers this? Auth, roles, ownership?
- **Interfaces & contracts** — inputs/outputs, request/response or prop shapes, events, error cases.
- **Behavior & states** — state machines, transitions, validations, edge cases, idempotency.
- **Dependencies** — other systems, services, libraries, tables, queues, third parties.
- **Non-functional** — performance, scale, security, limits, pagination, sizes, latency.
- **Acceptance criteria** — how do we know it's done? How is it verified/tested?
- **Testing** — what **unit tests** (logic, validation, edge cases in isolation) and **integration tests** (behavior across wired-together parts, end-to-end flows, contracts) are required? What's the minimum coverage bar per unit/case?
- **Constraints & non-goals** — tech constraints and things to deliberately NOT do.

**Backend lens:** endpoints/methods, data model, transactions, jobs, rate limits, observability.
**Frontend lens:** screens/components, state management, routing, data fetching, loading/error/empty states, responsiveness, accessibility, i18n, design system.
**Infra lens:** environments, deploy strategy, networking, scaling, secrets/config, IaC tooling, rollback, monitoring/alerting, cost.
**Database lens:** tables/collections, columns/types/nullability, keys, indexes, constraints, migrations + rollback, data backfill, retention.
**Data lens:** sources, transformations, schedule/triggers, schema, volume, quality checks, retention.

**Testing lens (every domain):** the plan must specify both **unit tests** and **integration tests** unless one is genuinely inapplicable (and then say why in Risks & open items). Unit tests cover logic, validation, and edge cases in isolation; integration tests exercise the parts wired together and the end-to-end behavior the spec promises.

### 3. Ask — relentlessly, but in batches
- Use the **AskUserQuestion** tool. Group related unknowns; ask the highest-impact ones first.
- Offer concrete options with a recommended default (marked "(Recommended)") so the user can decide fast, but never decide for them on a choice that changes the design.
- Keep looping: after answers, re-scan for new unknowns the answers introduce. Continue until the open-questions list is empty.
- Only stop asking when you can honestly state that no relevant checklist/lens item is unresolved.

### 4. Confirm understanding
Before writing the file, give a short (5–10 line) read-back of the resolved spec and the key decisions, and ask the user to confirm or correct. Write only after confirmation.

### 5. Write the plan
Save to `plans/<YYYY-MM-DD>-<kebab-slug>.md` (slug from the feature name). If a plan with that slug exists, append `-v2`, `-v3`, … rather than overwriting.

## Plan document template

Adapt section names to the domain (e.g. a frontend plan uses "Components & state" instead of "Data model"; an infra plan uses "Resources & topology"). Keep the numbered structure.

```markdown
# Plan: <Feature Name>

- **Date:** <YYYY-MM-DD>
- **Domain(s):** <backend | frontend | infra | database | ...>
- **Author:** plan-from-spec (reviewed with <user email if known>)
- **Status:** Draft

## 1. Summary
One paragraph: what we're building/changing and why.

## 2. Scope
### In scope
- ...
### Out of scope / non-goals
- ...

## 3. Resolved decisions
Every question asked and the answer chosen. The "nothing left to chance" record.
| # | Question | Decision |
|---|----------|----------|

## 4. Design
Domain-appropriate design detail:
- Backend → data model + API surface.
- Frontend → components, state, routing, data flow.
- Infra → resources, topology, environments, pipelines.
- Database → schema, columns/types/constraints, indexes, migrations + rollback.

## 5. Interfaces & contracts
Per interface: shape of inputs/outputs, errors, status/return codes, events.

## 6. Behavior & states
State machine(s), transitions, validations, edge cases, idempotency.

## 7. Implementation tasks
Ordered, small, independently shippable. For each: files/resources to add/change and why.
- [ ] Task 1 — ...
- [ ] Task 2 — ...

## 8. Testing
Both levels, explicit:
- **Unit tests** — units/cases to cover (logic, validation, edge cases), including failure paths.
- **Integration tests** — wired-together scenarios and end-to-end flows to cover, with their setup/teardown.
If either level is omitted, state why here.

## 9. Acceptance criteria
Verifiable outcomes and how each is tested/validated.

## 10. Risks & open items
Anything genuinely deferred (with rationale). Should be near-empty — if it's long,
you didn't ask enough.
```

## Rules

- **No implementation in this skill.** You produce a plan only. Building it is a separate step.
- **Fit the real project.** Reference actual files, conventions, tooling, and architecture found in the repo and `CLAUDE.md`,  `AGENTS.md` — whatever the stack. Don't impose patterns the repo doesn't use.
- **Tasks must be ordered and small** — each a sensible unit of work, dependencies first.
- **The "Resolved decisions" table is mandatory** — it's the proof nothing was left to chance.
- **Tests are mandatory.** Every plan must include both unit tests and integration tests (or justify in Risks & open items why a level doesn't apply), and the implementation tasks must contain explicit task(s) for writing them.
- If the user explicitly says "just assume sensible defaults", you may, but still record each assumption in the Resolved decisions table marked `(assumed)`.

## Output (chat)
After writing the file, reply with: the saved path, the domain(s), a 3–5 line summary of the plan, and the count of questions resolved. Offer to start implementing Task 1.
