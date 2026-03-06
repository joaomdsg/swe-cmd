# swe-refactor

Refactor code without altering observable behavior. Existing tests are the safety net. No new tests needed.

Usage: `/swe-refactor <description>`

---

## Before you start

Read:
1. `README.md` — build and test commands, project structure
2. `CONVENTIONS.md` — how this project does things and why
3. The affected code — understand what you're changing and why it's shaped this way

**Precondition:** run the full test suite before touching anything. All tests must be green. If tests are already failing, stop — do not refactor a broken codebase.

Do not begin until you understand the affected code. If anything is unclear, ask the human before proceeding.

---

## Reading depth

Not all code needs the same depth of understanding. Match depth to relevance:

**Depth 1 — Interface only**
Read exported functions, types, method signatures. Understand what it does and what contract it exposes.
Use for: adjacent modules you depend on but aren't modifying.

**Depth 2 — Logic**
Read the implementation. Understand control flow, edge cases, error paths.
Use for: code you're modifying or code directly affected by your change.

**Depth 3 — History**
Read git history, related specs, devlog entries. Understand why it's shaped this way.
Use for: code that looks wrong or surprising, or code with comments referencing incidents or decisions.

**Rule:** Never modify a file you've only read at Depth 1.

---

## Cycle

### RESEARCH

Read the affected code at the appropriate depth.

**Declare your working set:**

```
Working set:
- src/auth/session.ts [Depth 2 — modifying]
- src/auth/types.ts   [Depth 1 — interface only]
```

State: what are you changing, and why? What observable behavior must stay identical?

If you discover that the intended change would alter behavior, stop and surface this to the human before proceeding. A behavior change is not a refactor.

---

### CHANGE

Make the refactor.

**No new tests.** If you find yourself wanting to write a new test to cover something, that is a signal: either the existing tests have a gap (a separate concern), or the change is adding behavior (not a refactor).

Run the full test suite.

- **All green:** proceed.
- **Any failure:** stop. A broken test means the refactor changed behavior. Revert or reassess — do not suppress or rewrite the failing test.

---

### REVIEW

Scrutinize the change.

Review for:
- **Behavior preservation:** Are you confident no observable behavior changed? If uncertain, say so.
- **Clarity:** Is the refactored code more readable than before? If not, is there a better approach?
- **Quality:** Anything unnecessarily complex introduced? Anything that should be abstracted, or abstracted prematurely?
- **Conventions:** Evaluate against CONVENTIONS.md. Any divergence must be flagged.

Present findings with specifics. Human reviews and confirms or requests revisions.

Fix any issues. Run the full test suite. All tests must pass.

---

### DONE

Commit:
```
git commit -m "refactor(<area>): <summary>"
```

---

## What you are NOT doing

- Not writing new tests — if behavior is unchanged, existing tests are sufficient
- Not adding behavior under the guise of refactoring — any behavior change must be surfaced and approved
- Not suppressing or rewriting failing tests to make the suite appear green
- Not proceeding when the precondition (all-green suite) is not met
