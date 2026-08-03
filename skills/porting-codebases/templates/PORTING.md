# PORTING.md — {{SOURCE_LANG}} → {{TARGET_LANG}} translation rules

Companion to [`PORT-PLAN.md`](./PORT-PLAN.md). **Read this before every task.** Rules are
numbered so reviews can cite them (`violates R31`) instead of trading opinions.

- **Source tree:** `{{SOURCE_DIR}}`
- **Target tree:** `{{TARGET_DIR}}`
- **Status:** Draft. Amended during the trial run (Wave 0), frozen after. Amending it later
  requires the owner's sign-off, because everything translated before the change followed
  the old text.

Rule IDs are permanent. Add new rules at the end of their section with the next free
number; **never renumber**, never reuse a retired ID. Retired rules stay in the file struck
through with the date and reason.

---

## 0. Prime directives

These are identical in every port and are not negotiable.

- **R1.** This is a **mechanical port**. Preserve structure, declaration order, names,
  control flow, and embedded literals (SQL, regexes, format strings, error text) verbatim.
  The output should read as if the {{SOURCE_LANG}} file had been transpiled.
- **R2.** Do **not** refactor, rename, reorder, modernize, deduplicate, or "improve". Not
  even obviously-better versions. Idiomatic {{TARGET_LANG}} comes after the port ships.
- **R3.** Do **not** fix bugs you find in the {{SOURCE_LANG}} code. Record them under
  *Source defects* in `FINDINGS.md` and port the bug faithfully. A port that changes
  behavior cannot be validated against the original.
- **R4.** If this file conflicts with your judgment, **follow this file** and record the
  conflict under *Open questions* in `FINDINGS.md`.
- **R5.** If you cannot port something faithfully, emit a stub that fails loudly with
  `TODO(port): <reason>` and add an open question to `FINDINGS.md`. **Never guess an
  implementation.**
- **R6.** No new dependencies beyond the approved list in `PORT-PLAN.md` §3. If you think
  you need one, stop and ask.
- **R7.** {{VCS_POLICY}}
  <!-- Set by the interview. Either "Never run git — version control is the operator's."
       or a specific commit convention. Pick one; do not leave this ambiguous. -->
- **R8.** If a workaround needs a paragraph-long comment to justify it, the code is wrong —
  fix the code or stub it under R5.
- **R9.** Escape hatches ({{ESCAPE_HATCHES}}) are **expected** in a mechanical port. Use
  them rather than redesigning, and count them. They are a tracked metric, not a blocker.
- **R10.** Never widen visibility, relax a type, or add a parameter to make something
  easier to test. Test what the source exposes.

---

## 1. Files and symbols

- **R11.** One source file → one target file at the mapped path. Never merge, split, or
  invent a "shared" module that the source did not have.
  Mapping rule: {{PATH_MAPPING_RULE}}
  <!-- e.g. "backend/models/cliente.go → backend-node/src/models/cliente.ts —
       snake_case filenames preserved so the two trees stay greppable side by side." -->
- **R12.** Preserve declaration order inside the file. A reviewer reads both files in
  parallel; reordering destroys that.
- **R13.** Naming is the **only** transformation allowed. Casing conversion table:

  | {{SOURCE_LANG}} | {{TARGET_LANG}} |
  |---|---|
  | | |

  <!-- One row per construct kind: exported func, unexported func, method, type,
       constant, interface. Give a real example from this codebase in each row. -->
- **R14.** {{EXPORT_CONVENTION}}
- **R15.** Identifiers the source kept private stay private in the target.

---

## 2. Types

- **R16.** Primitive and container mapping table. Every type that appears in the source
  needs a row, with the exact import path where one is required.

  | {{SOURCE_LANG}} | {{TARGET_LANG}} | Import | Note |
  |---|---|---|---|
  | | | | |

- **R17.** Data-carrying aggregates → {{DTO_CONSTRUCT}}. Aggregates with behavior →
  {{SERVICE_CONSTRUCT}}.
- **R18.** Serialization field names are preserved **exactly** as the source emits them,
  including casing and any fields the source deliberately omits.
- **R19.** {{NULL_ABSENT_RULE}}
  <!-- The single highest-value type rule in most ports: how the source's
       zero-value / null / optional / absent distinction maps. Be concrete about what
       must never appear in output (e.g. "no `undefined` may reach JSON"). -->
- **R20.** {{DATE_TIME_RULE}}
  <!-- Representation, timezone, parse path, and the exact default for a null date. -->

---

## 3. Errors and control flow

- **R21.** {{ERROR_IDIOM_MAPPING}}
  <!-- How the source's error idiom becomes the target's: propagation, wrapping,
       and the sentinel/typed-error equivalents. -->
- **R22.** **Log-and-continue stays log-and-continue.** Where the source logs an error and
  proceeds, the target must proceed too — not throw, not abort the loop, not short-circuit
  the request.
- **R23.** Every error check in the source has a visible counterpart in the target. A
  reviewer must be able to count them and get the same number.
- **R24.** {{PANIC_ABORT_BOUNDARY}}
  <!-- What the source recovered from that the target aborts on, and vice versa. -->
- **R25.** Error messages and error codes are copied verbatim. They are part of the
  contract; callers and tests match on them.

---

## 4. Concurrency and async

- **R26.** {{TASK_SPAWN_MAPPING}}
  <!-- The source's "fire and forget" / spawn construct → the target's equivalent,
       and the requirement that it stay in the same position relative to surrounding
       statements (especially relative to writing a response). -->
- **R27.** {{BLOCKING_RULE}}
  <!-- Which source blocking calls are legal in the target's async context. -->
- **R28.** {{SYNCHRONIZATION_MAPPING}}
  <!-- Locks, channels, wait groups, cancellation, timeouts. -->

---

## 5. Resource ownership and cleanup

- **R29.** {{OWNERSHIP_RULE}}
  <!-- Who allocates, who frees/closes, and the lifetime of anything handed to a
       callback, thread, or foreign runtime. For GC'd targets this is still the
       file-handle / connection / transaction ownership rule. -->
- **R30.** Cleanup runs on **every** path, including the error path and including throws.
  Deferred-cleanup constructs in the source map to {{CLEANUP_CONSTRUCT}}.
- **R31.** {{TRANSACTION_RULE}}
  <!-- If the codebase has transactions: the single approved way to open one, and the
       prohibition on acquiring a second connection inside one. -->

---

## 6. Semantic divergence warnings

**The highest-value section in this file.** Constructs that look identical in
{{SOURCE_LANG}} and {{TARGET_LANG}} but behave differently. Populated by the pre-audit in
Phase 1 and appended to every time a divergence is discovered during the port.

Work the category checklist in `references/divergence-catalog.md` — do not freehand this.

| # | Class | {{SOURCE_LANG}} behavior | {{TARGET_LANG}} behavior | Rule to follow | Symptom if missed |
|---|---|---|---|---|---|
| D1 | | | | | |

Every row here is also a line item in the reviewer's priority list.

---

## 7. External surface

<!-- One subsection per boundary the codebase actually has. Delete the ones it doesn't.
     These rules are what keep clients working across the cutover. -->

### 7.1 {{BOUNDARY_1}}
- **R32.** …

### 7.2 {{BOUNDARY_2}}
- **R33.** …

---

## 8. Persistence

- **R34.** Query text is copied **byte for byte**. Indentation may change; nothing else may.
- **R35.** Placeholders and argument arrays stay aligned in count and order with the source.
- **R36.** {{DRIVER_COERCION_RULE}}
  <!-- How the target's driver coerces column types differently from the source's, and
       the explicit casts required to compensate. Verify this empirically against a real
       database before freezing the rule. -->

---

## 9. Tests

- **R37.** Source tests are ported in the same unit of work as the code they cover, at
  the mapped path {{TEST_PATH_MAPPING}}.
- **R38.** A test assertion that has to change is a **finding**, never a chore. Classify it
  in `FINDINGS.md` under *Test deltas* as **port defect** or **source implementation
  detail**. Never leave one unclassified — that is how silent behavior loss enters.
- **R39.** Never delete, skip, or `ignore` a test to make a wave green. Stub the code, not
  the test.

---

## 10. Stub and comment policy

- **R40.** A stub is: the correct signature, a loud failure carrying `TODO(port):` and the
  reason, and an open question in `FINDINGS.md`. Never a silent no-op, never a plausible
  guess.
- **R41.** Do not add explanatory comments the source did not have. The target should diff
  cleanly against the source. The one exception is `TODO(port):` markers.
- **R42.** Comments present in the source are carried over verbatim, including ones that
  are wrong or stale.
