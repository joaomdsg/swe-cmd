# swe-init

Phase Zero. Orient before you touch anything.

You are not building yet. You are reading, verifying, and forming an honest picture of the project's health. Your job in this phase is to understand what exists, flag what's missing or misleading, and ask the human whether to proceed or close gaps first.

---

## Steps

### 1. Read README.md

Read the project README.

Then verify it. Run the build command. Run the test command. Do they actually work?

Report what you found:
- Commands that work: state them.
- Commands that fail: show the error output.
- Commands that are missing: name the gap.

Do not silently fix anything yet.

### 2. Read CONVENTIONS.md

Read the project CONVENTIONS.md if it exists.

Flag any conventions that are stated as bare rules with no reasoning attached. A rule without a "why" is fragile — nobody knows whether it still applies, or whether breaking it matters.

Examples of bare rules:
- "Use single quotes." → Why? Linting rule? Historical incident? Team preference?
- "Never use global state." → Why? What went wrong? What's the alternative?

Note which sections have answered reasoning and which are empty or thin.

If CONVENTIONS.md includes an anti-pattern table, scan the codebase for any of the listed anti-patterns and report what you find.

### 3. Read existing tests

Read a representative sample of the existing tests.

Evaluate the comments:
- Do comments explain **why** a behavior matters, what failure mode it guards, or what non-obvious decision it encodes?
- Or do they describe **what** the test does — restating the assertion in prose?

A comment that says `// checks that status is 401` is noise. A comment that says `// guards against the session/token race from incident #47` is useful.

Report: what proportion of comments explain why? Which tests have misleading or redundant comments?

### 4. Build the codebase map

Read the directory structure, entry points, package manifests, and import patterns.

Write `.swe/map.md`:

```markdown
# Codebase Map

Generated: <date>

## Entry points
- <file> — <what it does>

## Module boundaries
- <module/dir> — owns [what]. Exposes: [public interface]. Depends on: [what].

## Dependency direction
<module> → <module> → <module> (list any violations of the intended direction)

## Hot paths
- <request/event> → <steps> → <outcome>

## Implicit conventions observed
- <pattern seen in the code that isn't written down anywhere>
```

Be honest about what you can't infer. A gap in the map is better than a wrong entry.

If `.swe/map.md` already exists, diff it against what you observe and update stale entries.

### 5. Create `.swe/` directory

If `.swe/` does not exist, create it:

```
.swe/
  map.md
  spec/
  _archive/
    superseded/
    deprecated/
```

If it already exists, note what's in it.

### 6. Define commit conventions

If the project does not already have commit message conventions, propose these:

```
spec(<name>): approved          — frozen spec committed
feat(<name>): B<N> — <summary> — behavior implementation complete
verify(<name>): all behaviors covered
```

Record them in CONVENTIONS.md if they are accepted. The agent will use these conventions throughout the workflow.

### 7. Report project health

Write a plain, honest summary:

- **README:** accurate / inaccurate / missing — with specifics
- **CONVENTIONS:** reasoned / bare rules / missing — with specifics
- **Tests:** test comment quality — with examples
- **Codebase map:** written / updated / gaps noted
- **`.swe/` directory:** created / already existed / what's in it

Do not soften gaps. Do not omit them to be polite. A gap you hide now costs more later.

### 8. Ask: proceed or close gaps first?

Present the health report to the human. Then ask:

> Based on this, do you want to proceed to feature specification, or address any of these gaps first?

Wait for a response before doing anything else.

---

## What you are NOT doing in this phase

- Not designing anything
- Not implementing anything
- Not writing specs
- Not making assumptions about what should change

You are reading and reporting. That's it.
