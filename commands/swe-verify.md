# swe-verify

Audit the gap between spec and tests. Close it explicitly.

This command is not optional at the end of a feature. It is the mechanism that enforces the spec↔test contract.

---

## Steps

### 1. Run the full test suite

Run the project's test command as stated in README.md.

Show the raw terminal output. Do not summarize it. The human needs to see the actual result.

If the suite fails: stop here. A failing suite is not a minor finding — it is the finding. Report it clearly and wait for the human to respond before proceeding.

### 2. Map behaviors to tests

Read `.swe/spec/feat-<name>/spec.md`.

For each behavior (B1, B2, B3, ...):
- Find the test(s) that cover it
- State the test name(s)
- State whether the coverage is complete, partial, or missing

Present a table:

| Behavior | Test(s) | Coverage |
|---|---|---|
| B1: <name> | `<test name>` | Complete |
| B2: <name> | `<test name>` | Partial — <what's missing> |
| B3: <name> | — | Missing |

### 3. Mutation audit

For each test mapped to a behavior, verify the test is actually constraining:

1. **Identify mutations.** What specific change to the production code should break this behavior but might not break this test? Name 1–2 concrete examples per test.

2. **Flag weak tests.** If you cannot find a mutation that the test would catch, the test is not constraining enough. Flag it.

This is the backstop for anything BLUE missed during implementation.

### 4. Audit test comments

For every test in scope, evaluate its comment (if any).

The only valid test comment explains **why** — the failure mode, the lesson, the non-obvious decision. A comment that describes **what** the test does or restates the assertions is noise.

Flag:
- Comments that describe what/how instead of why
- Comments that are accurate but redundant with the test name and assertions
- Tests where a comment would help but is absent (rare — most tests don't need one)

### 5. Flag convention divergence

Review the tests and implementation against CONVENTIONS.md.

Flag any divergence that was not already acknowledged in the devlog.

If CONVENTIONS.md includes an anti-pattern table, scan for violations now.

### 6. Close the gap

For every gap identified — missing coverage, weak tests, incorrect coverage, misleading comments — you must close it now or get an explicit human decision to defer it.

Present the gaps. For each one:

> Gap: B3 has no test. Do you want me to write it now, or defer with a note in the devlog?

Do not silently leave gaps open.

### 7. Final report and commit

After gaps are addressed:

- Full test suite passing: yes/no
- All behaviors covered: yes/no (with any acknowledged deferrals noted)
- Mutation audit: clean / issues resolved
- Test comment quality: clean / issues resolved / known exceptions
- Convention divergence: none / resolved / acknowledged in devlog

If everything is green, commit:

```
git commit -m "verify(<spec-name>): all behaviors covered"
```

Log to the devlog:
```markdown
## <date> — Verified
Commit: <hash>. Coverage: [summary]. Any deferrals: [or none].
```

---

## What this command enforces

A spec behavior with no test is a promise with no verification. It will silently break.

A test with no corresponding spec behavior is a test that cannot be reviewed for correctness — there is no contract to measure it against.

This command closes both gaps.
