# swe-spec

Specify a feature as a set of verifiable behaviors. Nothing gets implemented without a frozen spec.

---

## Steps

### 1. Human describes the feature

The human gives you a description — rough, partial, or detailed. It doesn't matter. Your job is to turn it into something precise.

### 1b. Research before interviewing

Before asking your first question, understand the domain:

1. Read `DESIGN.md` — domain context and past tradeoffs.
2. Read the "Things We've Learned the Hard Way" section of `CONVENTIONS.md`.
3. Read existing specs in `.swe/spec/` for related areas — including archived ones. Superseded specs are especially valuable: they show what went wrong before.
4. Read `.swe/map.md` to understand which modules this feature will touch.

Use what you find to ask better questions. The goal is to ask about failure modes that exist in *this specific system*, not generic edge cases.

### 2. Interview

Ask clarifying questions. Do not write the spec yet.

Your questions should:
- Surface assumptions the human is making (and hasn't stated)
- Identify edge cases that haven't been considered
- Clarify what "correct" behavior looks like in ambiguous situations
- Establish what this feature explicitly does NOT do

Always ask:
- "What has gone wrong before with something like this, in this codebase or elsewhere?"
- "What's the hardest part of this to get right?"
- "What are you NOT building here?"
- "What should happen if X is missing/empty/invalid?"
- "What does the failure case look like, and what should the system do?"

Ask as many rounds as needed. The spec is wrong if the behaviors surprise the human when they read them back.

### 3. Translate to behaviors

Write the feature as a list of verifiable behaviors in BDD format.

Each behavior:

```
**Behavior:** <one-line description — what the system does, not how>

**Given:** <the precondition>
**When:** <the action or event>
**Then:** <the observable outcome>

**Guards against:** <what failure mode or incorrect behavior this prevents>

**Notes:** <any non-obvious context, edge cases, or decisions captured here>
```

Behaviors must be:
- Observable from the outside (no implementation details)
- Testable (you can write a test that unambiguously passes or fails)
- Atomic (one thing per behavior — split if needed)

### 4. Write spec.md

Create the spec file at:

```
.swe/spec/feat-<behavior-centric-name>/spec.md
```

The name should describe what the feature does, not how it's built. Prefer `feat-user-session-expiry` over `feat-auth-refactor`.

Use this structure:

```markdown
---
feature: <behavior-centric-name>
status: draft
created: <date>
approved: null
superseded_by: null
---

# <Feature Name>

<One paragraph describing the feature's purpose and scope.>

## Behaviors

### B1: <Behavior name>

**Given:** ...
**When:** ...
**Then:** ...

**Guards against:** ...

**Notes:** ...

### B2: <Behavior name>

...

## Impact

### Modules affected
- <module> — <what changes and why>

### Specs affected
- <feat-name> — <how this feature interacts with or affects it>

### Integration risks
- <anything that could break, interfaces that change, callers affected>

## Failure Modes

What could go wrong? Surface what the interview revealed.

| Failure mode | Likelihood | Mitigation |
|---|---|---|
| | | |
```

### 5. Create devlog.md

Create alongside the spec:

```
.swe/spec/feat-<behavior-centric-name>/devlog.md
```

```markdown
---
feature: <behavior-centric-name>
spec: ./spec.md
started: <date>
status: specifying
---

# Devlog: <Feature Name>

## <date> — Spec drafted

Initial behaviors written from interview. [Note key decisions made during the interview, assumptions surfaced, failure modes identified.]
```

### 6. Human review

Present the spec to the human.

Say: "Please review. Does this match your intent? Are any behaviors missing, wrong, or out of scope? I'll wait for your approval before this spec is frozen."

Incorporate feedback. Repeat until the human approves.

### 7. Freeze the spec

On human approval:

1. Update spec.md frontmatter: `status: approved`, `approved: <date>`
2. Update devlog.md: `status: implementing`, add entry:

```markdown
## <date> — Spec approved

Approved by human. Spec is now frozen. Implementation may begin.
```

3. Commit:
```
git commit -m "spec(<spec-name>): approved"
```

Log the commit hash in the devlog:
```markdown
Commit: <hash>
```

The spec is now frozen. **Do not modify spec.md behaviors after approval without running swe-respec.**

---

## What frozen means

A frozen spec is a contract. The behaviors in it are what get implemented and tested. If the contract needs to change, that change is explicit — via swe-respec — not silent.

Changing a frozen spec without going through swe-respec is the equivalent of changing the requirements after the work is done and not telling anyone.
