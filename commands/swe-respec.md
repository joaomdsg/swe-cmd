# swe-respec

Supersede or deprecate a frozen spec. Forces acknowledgment that you are changing or retiring a contract.

You cannot silently modify a frozen spec. This command is the mechanism for doing it explicitly.

---

## When to use this

**Supersede** — the feature needs to change. A new spec replaces the old one. The old spec is archived, not deleted. The history is preserved.

**Deprecate** — the feature is being retired. No replacement. The spec is archived to document what used to exist and why it was removed.

---

## Steps

### 1. Identify the spec

Confirm the feature name and location:

```
.swe/spec/feat-<name>/spec.md
```

Read the current spec. Read the devlog. Understand what was built and why before you change or retire it.

### 2. Human states intent

Ask the human:

> Are you superseding this spec (replacing it with a new version) or deprecating it (retiring it with no replacement)?

Wait for a clear answer.

If superseding: ask what changes. Run a focused interview if the new scope is unclear — same as swe-spec step 2.

If deprecating: ask why. The reason belongs in the archive.

### 3a. Supersede

1. Update the old spec.md frontmatter:
   ```yaml
   status: superseded
   superseded_by: feat-<new-name>
   ```

2. Move the old spec directory to the archive:
   ```
   .swe/_archive/superseded/feat-<old-name>/
   ```
   Move the entire directory (spec.md and devlog.md).

3. Add a devlog entry before moving:
   ```markdown
   ## <date> — Superseded

   Superseded by feat-<new-name>. Reason: <brief reason from human>.
   ```

4. Run swe-spec to create the new spec from scratch. The new spec is a fresh contract — it is not the old spec with edits.

### 3b. Deprecate

1. Update the spec.md frontmatter:
   ```yaml
   status: deprecated
   ```

2. Add a devlog entry:
   ```markdown
   ## <date> — Deprecated

   Retired with no replacement. Reason: <reason from human>.
   ```

3. Move the spec directory to the archive:
   ```
   .swe/_archive/deprecated/feat-<name>/
   ```

### 4. Confirm

Report to the human:

- What was archived and where
- If superseding: confirm the new spec process is starting

---

## What you are preserving

The archive is not a trash folder. It is a record of what was decided, what was built, and why it changed or ended.

Future contributors — or future you — may need to understand why a feature was retired or replaced. The archive makes that traceable.

Do not delete archived specs.
