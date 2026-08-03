# Semantic divergence catalog

Constructs that look identical in two languages and behave differently. These are what
produce port regressions: syntactically faithful translations with different semantics.
They survive review precisely because the code *looks* right.

**How to use this.** In Phase 1, walk every category. For each: grep the source for the
construct, decide whether the pair diverges, and either write a `PORTING.md` §6 row with
a rule, or record `n-a` in `PORT-PLAN.md` §6. Working the list is what makes two runs of
the audit converge — a freehand hunt finds a different subset every time.

For each divergence you confirm, write the row as: **what the source does · what the
target does · the rule to follow · the symptom if missed.** A row without a rule is a
note, not a finding.

**Probe, do not assume.** For anything involving a driver, a serializer, or a formatter,
write a five-line script and run it against the real dependency. Documented behavior and
actual behavior part ways most often exactly here.

---

## 1. Numbers

- Integer overflow: wraps, panics, promotes to bignum, or is undefined?
- Division and modulo on negatives: truncation toward zero vs. flooring. Both languages
  round `-7/2` somewhere; check they agree.
- Integer division that silently becomes float division in the target.
- Fixed-width vs. arbitrary-precision integers. Any value above 2^53 crossing a JSON
  boundary.
- Float formatting: how many digits does each language print by default? Does the target
  print `1` where the source printed `1.0`?
- Decimal / money types. Does the DB driver hand back a string, a float, or a decimal?
  A float here is a defect.
- Rounding mode: half-up vs. banker's rounding.
- NaN and infinity: comparison, sorting, serialization.

## 2. Strings and encoding

- Indexing unit: bytes, code units, or code points. A loop over "characters" rarely means
  the same thing twice.
- UTF-8 vs. UTF-16 vs. raw bytes as the internal representation.
- Invalid sequences: replaced, rejected, or preserved?
- Case conversion under a locale (the Turkish dotless-ı class of bug).
- Trailing/leading whitespace handling in trims; which code points count as whitespace.
- String comparison and sort order: byte order vs. collation.
- Format-string semantics: argument order, padding, precision, and whether the format
  string is evaluated at compile time or runtime.
- Regex dialect: greedy/lazy defaults, `.` matching newline, anchors in multiline mode,
  backreference syntax, Unicode property support.

## 3. Null, absent, and zero

**The highest-yield category in most business codebases.**

- Does the source distinguish "absent" from "zero value"? Does the target?
- Where the source has one concept, does the target have two (`null` *and* `undefined`)?
  Which one reaches serialization?
- A field the source always emitted with a zero value that the target omits entirely.
  Clients break silently on this.
- Nullable columns: does the driver produce the language's null, or a zero value?
- Optional/maybe wrappers: what does the target do with a missing key — throw, or yield
  the empty value?
- **Round-tripping conventions**: codebases that read NULL as `0` and write `0` back as
  NULL. Grep for it; it is invisible in any single function.

## 4. Collections

- Map/dict iteration order: guaranteed, insertion-ordered, or deliberately randomized?
  Tests accidentally depend on this constantly.
- Empty vs. null collections at the serialization boundary: `[]` vs. `null`.
- Value vs. reference semantics on assignment and on passing to a function. Does the
  target alias where the source copied?
- Default values for missing keys vs. raising.
- Behavior on out-of-range index: exception, clamp, wrap, or undefined.
- Slicing with negative or out-of-range bounds.
- Sort stability, and comparator semantics for equal elements.
- Set and map key equality: structural or identity? Which types are hashable?

## 5. Evaluation and control flow

- Eager vs. lazy argument evaluation. A "use this default on failure" helper whose
  argument is evaluated unconditionally will fail before the fallback can apply.
- Short-circuit semantics of boolean operators, and what each language considers truthy.
  Is `0` truthy? `""`? An empty collection?
- Operator overloading and implicit conversions in comparisons.
- Statement-level scoping: loop variable capture in closures created inside a loop.
- Order of evaluation of function arguments and of compound assignments.
- Fallthrough behavior in switch/match constructs.
- Integer/float comparison across types.

## 6. Errors, panics, and assertions

- What the source recovers from that the target aborts on, and the reverse.
- Assertions and invariant checks that are **erased in release builds**. If a call inside
  an assertion has a side effect, the side effect disappears in production.
- Debug-only logging that carries side effects.
- Whether an unhandled error in a background task kills the process or is swallowed.
- Error identity: sentinel comparison vs. type matching vs. string matching.
- Stack unwinding and whether cleanup handlers run on the abort path.
- **Error-return vs. exception:** callers who ignored a returned error become
  unhandled-rejection sites when the target throws. This changes control flow at every
  call site, not just at the definition.

## 7. Concurrency

- Is blocking legal in the target's async context? A synchronous call that was fine in the
  source may stall an event loop.
- Task spawn semantics: detached vs. joined, and what happens to the result and to errors.
- Cancellation and timeout propagation.
- Memory model and visibility guarantees for shared state.
- Reentrancy of locks.
- Whether "run this in the background" runs before or after the response is written. The
  ordering relative to the response is observable.
- Thread-local / context-local storage equivalence.

## 8. Time

- Timezone handling: is the source using UTC, local, or a fixed offset? Where is it set —
  process env, DB session, or per-call?
- Monotonic vs. wall clock for durations.
- Precision: seconds, milliseconds, nanoseconds — and truncation when crossing a boundary.
- Date arithmetic across DST transitions and month boundaries.
- Serialization format: exact string form, offset notation, and what a zero/null date
  serializes to.
- Parsing leniency: does the target accept inputs the source rejected?

## 9. Resources and cleanup

- Deterministic destruction vs. garbage collection. Anything holding a file handle,
  socket, or DB connection.
- Order of deferred/scoped cleanup: LIFO, FIFO, or unspecified.
- Cleanup on the error path, and double-cleanup.
- Ownership across a callback, thread, or FFI boundary — who frees, and when.
- Connection pool semantics: does the target check a connection out per query where the
  source held one per request? Transactions break subtly on this.

## 10. Serialization boundary

- Field naming and casing. Automatic case converters mangle acronyms — `tipoDocumentoID`
  becomes `tipoDocumentoId`.
- Fields deliberately omitted from output in the source.
- Nested vs. flattened representation of embedded/inherited structures.
- Numeric precision and large-integer handling in JSON.
- Whitespace and indentation, if anything downstream hashes or snapshots responses.
- Key ordering, if anything downstream compares serialized text.
- How the empty value of each type serializes.

## 11. Persistence

- Placeholder syntax (`?` vs. `%s` vs. `$1`) and escaping of literal placeholder
  characters inside query text — `LIKE '%...'` is the classic break.
- Driver type coercion: what the driver returns for DECIMAL, BOOLEAN/TINYINT, BLOB, JSON,
  and date columns. **Probe this against a real database; do not trust the docs.**
- Implicit transactions and autocommit defaults.
- Isolation level defaults.
- Prepared-statement vs. client-side interpolation, and whether that changes type coercion.
- Multi-statement support and its default.
- Session settings the source sets on connect (timezone, charset, sql_mode) that the
  target must set identically.

## 12. Build and packaging

- Assets the source embeds at compile time that the target must load at runtime — and the
  path it loads them from.
- CWD-relative paths that break when the working directory changes.
- Environment variable names, defaults, and parsing (is `"false"` falsy?).
- Conditional compilation in the source with no target equivalent.
- Whether the target's release mode changes any semantics from its debug mode.

---

## Recording the audit

In `PORTING.md` §6:

| # | Class | Source behavior | Target behavior | Rule to follow | Symptom if missed |
|---|---|---|---|---|---|
| D1 | Null vs. zero | NULL int column scans to `0`; `0` is written back as NULL | driver yields `None`; serializer emits `null` | Map NULL→`0` in the row mapper and `0`→NULL on write, per model. Never let `None` reach output. | Client reads `null` where it always received `0`; conditional rendering breaks with no error |

Then add every row to the reviewer's priority list. A divergence that is audited but not
in the reviewer prompt gets found in production instead of in review.
