# swe-implement

Implement a feature by driving each behavior through a micro TDD cycle.

Usage: `/swe-implement <feature-name>`

The feature name matches a directory under `.swe/spec/feat-<name>/`.

---

## Before you start

Read:
1. `README.md` — build and test commands, project structure
2. `CONVENTIONS.md` — how this project does things and why
3. `.swe/map.md` — codebase structure and module boundaries (if it exists)
4. `.swe/spec/feat-<name>/spec.md` — the frozen spec you are implementing
5. `.swe/spec/feat-<name>/devlog.md` — prior decisions and current position
6. Existing tests — understand the testing patterns in use

The spec must have `status: approved`. If it is still `draft`, stop and run swe-spec to get it approved first.

**Resuming mid-feature:** If the devlog shows prior progress, find the last commit hash logged. Run `git log --oneline` to confirm position. Begin from the behavior after the last completed one.

Do not begin until you have a clear picture of the landscape. If anything is unclear, ask the human before proceeding.

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
Use for: code that looks wrong or surprising, code you're tempted to refactor, code with comments referencing incidents or decisions.

**Rule:** Never modify a file you've only read at Depth 1. If you discover mid-implementation that you need to change a Depth-1 file, stop, promote it to Depth 2, re-read, and update your plan before continuing.

---

## Cycle

For **each behavior** in the spec, run the full cycle. Do not skip phases.

Work through behaviors in order: B1, B2, B3, ...

**Between behaviors:** clearing context and re-reading only the relevant files can help avoid carrying stale assumptions forward. Suggest this to the human when the context feels heavy — but don't enforce it.

---

### RESEARCH

Re-read the behavior you are about to implement.

**Declare your working set.** Identify which files you expect to read and modify:

```
Working set:
- src/auth/session.ts [Depth 2 — modifying]
- src/auth/types.ts   [Depth 2 — affected]
- src/db/queries.ts   [Depth 1 — interface only]
```

Read each file at the declared depth. For Depth 2 files, state your understanding:
- What does it do?
- Why is it shaped this way? (look at comments, test names, git history if needed)
- What constraints does it impose on your change?
- What would break if you changed it?

If you cannot explain *why* existing code is shaped the way it is, say so explicitly. That is a blind spot the human should know about.

**PAUSE if scope expands.** If you discover mid-research that you need to touch files outside your declared working set, stop. Present the expanded blast radius to the human and get explicit approval before continuing.

Log to devlog:
```
## <date> — B<N> research
Working set: [files]. Key constraint: [anything non-obvious].
```

---

### PLAN

State your implementation plan for this behavior:
- What test you will write and why
- What production code you will write
- Any decisions or tradeoffs you're making

If your plan diverges from CONVENTIONS.md, flag it explicitly now. Do not wait until REVIEW.

Present the plan. Wait for acknowledgment before proceeding.

Log to devlog:
```
## <date> — B<N> plan
Approach: [summary]. Convention divergence (if any): [what and why].
```

---

### RED

Write the failing test.

Run the full test suite. Verify:
1. The new test compiles (if applicable)
2. The new test fails
3. **The test fails for the correct reason** — the failure message demonstrates that the test is actually exercising the behavior, not failing due to a setup error, wrong assertion, or test bug

If the test fails for the wrong reason, fix it until it fails for the right reason. A test that fails incorrectly will pass incorrectly once code is written.

Do not proceed to GREEN until the failure is correct.

Log to devlog:
```
## <date> — B<N> red
Test: [file:line]. Fails: [reason — confirm it's the right reason].
```

---

### GREEN

Write just enough code to make the test pass.

Run the full test suite. All tests must pass — including pre-existing tests.

If a pre-existing test breaks, investigate. Do not suppress it. Do not skip it. Understand why it broke and fix the root cause.

**Prune.** Look at every line you wrote. For each line ask: "If I delete this line, does the new test fail?"
- If yes: the line is necessary. Keep it.
- If no: the line is premature. Remove it. It belongs to a future behavior.

Common things to prune: error handling for cases no test exercises yet, parameters no test uses, branches for conditions no test triggers.

Log to devlog:
```
## <date> — B<N> green
Modified: [files]. Approach: [brief]. Pruned: [anything removed and why].
```

---

### BLUE

Scrutinize the test code.

**Try to break your own test first:**

1. **Mutation check.** Identify 2–3 specific changes to the production code that should break the behavior but might NOT break your test. Example: "If I returned null instead of throwing, would this test catch it?" If you find a gap, fix the test.

2. **False pass check.** Could this test pass with a trivially wrong implementation — a hardcoded return value, an always-true condition? If yes, the test is not constraining enough.

3. **Behavior vs. implementation check.** If you replaced the implementation with a completely different approach producing the same observable outcome, would the test still pass? It should. If it wouldn't, the test is coupled to implementation.

**Then evaluate:**
- Does the test name describe the behavior without requiring you to read the body? Yes/No.
- Does the test reference any internal function name, private field, or implementation detail? List them.
- If there is a comment, does it explain *why* — the failure mode, the non-obvious decision? Or does it restate what the assertions already show? If the latter, remove it.
- Is any pre-existing test now redundant or contradictory in light of this new test?

Present findings with specifics. Human reviews and confirms or requests revisions.

Fix any issues. Re-run the full suite.

Log to devlog:
```
## <date> — B<N> blue
Mutations checked: [list]. Gaps found and fixed: [or none]. Human changes: [or none].
```

---

### REVIEW

Scrutinize the production code.

Review for:
- **Correctness:** Does this code actually implement the behavior? Are there edge cases the spec describes that this code doesn't handle?
- **Clarity:** Is the intent readable without a comment?
- **Quality:** Anything unnecessarily complex? Anything copied that should be abstracted, or abstracted prematurely?
- **Security:** Injection, improper input handling, insecure defaults, exposed internals.
- **Conventions:** Evaluate against CONVENTIONS.md. Any divergence must be flagged — including whether the divergence is justified.

Present findings with specifics. Human reviews and confirms or requests revisions.

Fix any issues. Run the full test suite. All tests must pass.

Log to devlog:
```
## <date> — B<N> review
Production code findings: [or none]. Convention divergence: [or none]. Human changes: [or none].
```

---

### DONE

Commit:
```
git commit -m "feat(<spec-name>): B<N> — <behavior summary>"
```

**Commit granularity:** committing after each behavior is the default. If several behaviors are tightly coupled or trivially small, suggest batching them into one commit — but let the human decide.

Log to devlog:
```
## <date> — B<N> done
Commit: <hash>. [Any decision, block, override, or divergence worth preserving. If nothing notable, write "no notable decisions".]
```

The devlog entry must be sufficient to resume from cold: what was done, what was decided, and where things stand.

Move to the next behavior and begin from RESEARCH.

---

## When all behaviors are complete

Run the full test suite one final time. Show the output.

Update spec.md frontmatter: `status: implemented`

Log to devlog:
```
## <date> — Implementation complete
All behaviors implemented. Suite: [passing]. Open items: [or none].
```

Report to the human:
- All behaviors implemented
- Test suite status
- Any open items surfaced during implementation

Run `swe-verify` before considering the feature done.

---

## What you are NOT doing

- Not implementing behaviors that aren't in the spec
- Not skipping phases to go faster
- Not silently reinterpreting the spec when the code suggests something different
- Not writing a test and immediately writing the code without verifying the test fails first
- Not writing a comment that restates what the test already shows
- Not committing at RED — a failing test is not worth preserving
