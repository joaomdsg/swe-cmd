# swe-status

Standalone health audit. Run anytime — after a session, before starting work, or when something feels off.

This command has no side effects. It reads and reports. It does not change anything.

---

## Steps

### 1. README accuracy

Read README.md. Run the build command. Run the test command.

Report:
- Commands that work
- Commands that fail (show output)
- Commands that are missing or outdated

A stale README is actively harmful — it wastes time and erodes trust in documentation.

### 2. Test comment quality

Scan the test suite for comments.

For each comment found, classify it:
- **Why** — explains a failure mode, lesson, or non-obvious decision. Useful.
- **What/How** — restates what the test does or how it does it. Noise.
- **Stale** — no longer accurate given the current code or behavior.

Report: count of each type, with specific examples of the problematic ones.

### 3. Convention health

Read CONVENTIONS.md.

Flag:
- Sections with bare rules and no reasoning
- Sections that are empty or placeholder
- Conventions that appear to contradict observed patterns in the codebase
- Conventions that reference outdated practices or tools

A thin CONVENTIONS.md on day one is honest. A thin one after six months of development is a gap.

### 4. Spec coverage

For each spec in `.swe/spec/`:

Read the spec. Check its status. For approved or implemented specs, check whether corresponding tests exist.

Report:
- Specs with `status: approved` but no tests (implementation not started or spec not followed)
- Specs with `status: implemented` but gaps in test coverage
- Specs with `status: draft` that appear to be stale (old creation date, no recent devlog activity)

### 5. Test vs. behavior alignment

For a representative sample of tests (or all, if the suite is small):

Evaluate whether each test specifies a **behavior** or mirrors an **implementation**.

Signs a test mirrors implementation rather than specifying behavior:
- The test name describes internal function calls or method names
- The test breaks when internal structure changes even though observable behavior is unchanged
- The test asserts on private state rather than public outcomes
- The test is tightly coupled to a specific implementation strategy

Report examples of tests that are over-specified or implementation-coupled.

### 6. Devlog freshness

Scan `.swe/spec/*/devlog.md` for specs with `status: implementing` or `status: specifying`.

Flag any that have no recent entries. An in-progress spec with a stale devlog suggests either:
- Work is happening without being logged (loss of decision record)
- The spec is abandoned without being formally deprecated

### 7. Summary

Produce a plain summary:

```
README:       accurate | stale | broken — <specifics>
Test comments: clean | N what/how comments — <examples>
Conventions:  reasoned | N bare rules | thin — <specifics>
Spec coverage: N specs, N fully covered, N gaps — <list>
Test quality: N implementation-mirroring tests — <examples>
Devlogs:      N active, N stale — <list>
```

State the most important thing to address first, if anything stands out.

---

## What this command is not

This command does not fix anything. It does not write tests, update comments, or modify specs. It reads and reports so that a human can make informed decisions about what to address.

Run it as often as useful.
