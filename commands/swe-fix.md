# swe-fix

Fix a bug using TDD discipline. No spec, no interview, no freeze. The test IS the spec.

Usage: `/swe-fix <description>`

---

## Before you start

Read:
1. `README.md` — build and test commands, project structure
2. `CONVENTIONS.md` — how this project does things and why
3. Relevant code — identify the area where the bug lives

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
Use for: code that looks wrong or surprising, or code where the bug's origin matters.

**Rule:** Never modify a file you've only read at Depth 1.

---

## Cycle

### RESEARCH

Read the affected code. Reproduce the bug — confirm you understand exactly when and why it occurs.

**Declare your working set:**

```
Working set:
- src/auth/session.ts [Depth 2 — modifying]
- src/auth/types.ts   [Depth 1 — interface only]
```

State: what is the bug? What is the expected behavior? What is the actual behavior?

If the root cause is unclear after reading, say so explicitly before proceeding.

---

### RED

Write a failing test that captures the bug.

The test must:
- Fail before the fix
- Fail for the right reason — the failure message should demonstrate the buggy behavior, not a setup error
- Pass after the fix (and only after)

Run the full test suite. Verify the new test fails and all pre-existing tests pass.

Do not proceed to GREEN until the failure is correct.

---

### GREEN

Fix the bug. Write just enough code to make the test pass.

Run the full test suite. All tests must pass — including pre-existing tests.

If a pre-existing test breaks, investigate. Do not suppress it. Do not skip it. Understand why it broke and fix the root cause.

**Prune.** Look at every line you wrote. If deleting a line doesn't break the new test, remove it.

---

### BLUE

Scrutinize the test code.

**Try to break your own test first:**

1. **Mutation check.** Identify 2–3 specific changes to the production code that should reintroduce the bug but might NOT break your test. If you find a gap, fix the test.

2. **False pass check.** Could this test pass with a trivially wrong implementation — a hardcoded return value, an always-true condition? If yes, the test is not constraining enough.

3. **Behavior vs. implementation check.** If you replaced the implementation with a completely different approach producing the same observable outcome, would the test still pass? It should. If it wouldn't, the test is coupled to implementation.

**Then evaluate:**
- Does the test name describe the bug and the correct behavior without requiring you to read the body? Yes/No.
- Does the test reference any internal function name, private field, or implementation detail? List them.
- If there is a comment, does it explain *why* — the failure mode, the non-obvious decision? Or does it restate what the assertions already show? If the latter, remove it.

Present findings with specifics. Human reviews and confirms or requests revisions.

Fix any issues. Re-run the full suite.

---

### REVIEW

Scrutinize the fix.

Review for:
- **Correctness:** Does this code actually fix the bug? Are there related edge cases this fix should also handle?
- **Root cause:** Is this a genuine fix or a symptom patch? If it's patching a symptom, flag it explicitly.
- **Clarity:** Is the intent readable without a comment?
- **Quality:** Anything unnecessarily complex? Anything that should be abstracted?
- **Security:** Injection, improper input handling, insecure defaults, exposed internals.
- **Conventions:** Evaluate against CONVENTIONS.md. Any divergence must be flagged.

Present findings with specifics. Human reviews and confirms or requests revisions.

Fix any issues. Run the full test suite. All tests must pass.

---

### DONE

Commit:
```
git commit -m "fix(<area>): <summary>"
```

If a `.swe/spec/` feature is affected by this fix, add a brief devlog entry to that spec's `devlog.md`:
```
## <date> — bug fix
<summary of bug and fix>. Commit: <hash>.
```

---

## What you are NOT doing

- Not skipping RED — a fix without a failing test first is not verified
- Not writing a test after the fix — the test must fail before the fix exists
- Not suppressing pre-existing failures to make the suite appear green
- Not patching symptoms without flagging that the root cause remains
