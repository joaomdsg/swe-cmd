# swe-cmd

Discipline is the source of speed, not the enemy of it.

---

## The problem nobody wants to name

Every AI coding tool in 2026 is obsessed with context engineering — curating what the model sees so it produces better output. Embeddings, RAG pipelines, dynamic rule loading, memory systems. All of it assumes the same thing: if the model sees the right information, it will do the right thing.

It won't.

A model with perfect context and no process will still:
- Implement what it inferred instead of what you meant
- Write tests that mirror the implementation instead of specifying behavior
- Leave no record of decisions, so every question gets re-litigated
- Drift from conventions without noticing, and without leaving a trail

Context engineering answers "what should the model see?" Discipline answers "what should the model *do* with what it sees?" The industry is solving the first question and ignoring the second.

This repository is the second answer.

---

## What this is

A methodology for agentic software development, encoded as executable commands. Not a framework, not a product, not a scaffold. A set of habits — given shape so an agent can follow them and a human can audit them.

The core insight: **design for humans and the agent benefits for free.** Readable specs, honest conventions, and meaningful tests serve the human first. The agent inherits the clarity at no extra cost.

---

## Key ideas

### Reading depth prevents blind modifications

Three levels: Interface → Logic → History. One hard rule: **never modify a file you've only read at Depth 1.**

This is a simple constraint that prevents one of the most common agent failures: modifying code it doesn't understand. No other workflow encodes this.

### Conventions are questions, not rules

Every CLAUDE.md, AGENTS.md, and cursor rules file in the wild is a list of directives. "Use single quotes." "Never use global state." Bare rules with no reasoning.

swe-cmd's CONVENTIONS.md template forces you to answer *why*: "Where have errors bitten you before?" "What makes a test brittle in this project?" This produces conventions an agent can reason about, not just obey. A rule without a why is fragile — nobody knows whether breaking it matters.

### The BLUE phase catches what RED→GREEN misses

TDD's standard loop is RED (write a failing test) → GREEN (make it pass) → refactor. swe-cmd extends it: RED → GREEN → BLUE → REVIEW.

BLUE is where you try to break your own test:
- **Mutation check.** What change to production code should break this but won't break the test?
- **False pass check.** Could a hardcoded return value pass this test?
- **Behavior vs. implementation check.** Would a completely different implementation still pass?

Tests that pass for the wrong reason are worse than no tests. They give you false confidence. BLUE kills them.

### The devlog.md is memory that doesn't evaporate

Chat history disappears. Task lists get stale. swe-cmd writes a devlog.md entry per behavior, per phase — with commit hashes, decisions made, conventions diverged from, and blind spots flagged.

Start a new session six months later, read the devlog.md, and you know exactly where things stand and why they're shaped that way. No embeddings required.

---

## The honest gap

swe-cmd optimizes the *process layer* — what the agent does and in what order. It does not optimize the *information layer* — what the agent sees at each step.

The strongest version of this would be swe-cmd's workflow running inside a context-engineered agent that dynamically loads the right spec.md section, the right devlog.md entry, and the right conventions based on which behavior is currently being implemented. Instead of the agent manually reading everything, the system serves exactly what's needed.

That's not built yet. Right now, it's plain markdown files and a disciplined agent. And that still beats a context-optimized agent with no discipline.

---

## Commands

| Command | Purpose |
|---|---|
| `/swe-init` | Phase Zero — orient, read the project, establish conventions, create `.swe/` |
| `/swe-spec` | Interview → behaviors → frozen spec.md |
| `/swe-respec` | Supersede or deprecate an existing spec.md |
| `/swe-implement <feature>` | Micro TDD cycle per behavior |
| `/swe-fix <description>` | Fix a bug with TDD — no spec.md, test IS the spec.md |
| `/swe-refactor <description>` | Refactor without altering behavior — existing tests are the safety net |
| `/swe-verify` | spec.md↔test coverage audit |
| `/swe-status` | Standalone health audit, run anytime |

---

## What gets created in your project

Running `/swe-init` creates a `.swe/` directory:

```
.swe/
  map.md
  spec/
    feat-<behavior-centric-name>/
      spec.md       # BDD behaviors. Frozen when approved. Directory named after the feature being specified.
      devlog.md     # Decisions, blocks, overrides, divergence.
  _archive/
    superseded/
    deprecated/
```

---

## Typical workflow

```
/swe-init           # once per project
/swe-spec           # interview → behaviors → frozen spec.md
/swe-implement      # TDD cycle per behavior (RED → GREEN → BLUE → REVIEW)
/swe-verify         # spec.md↔test coverage audit before shipping
/swe-status         # run anytime to catch drift
```

---

## Prerequisites

- [OpenCode](https://opencode.ai) installed and on your PATH

---

## Installation

These are [OpenCode](https://opencode.ai)-compatible markdown commands (OpenCode also picks up Claude Code and Cursor context files). Install by placing the `commands/` directory where OpenCode resolves custom commands, or symlinking:

```sh
git clone https://github.com/joaomdsg/swe-cmd.git
ln -s /path/to/swe-cmd/commands ~/.config/opencode/commands
```

Then in any project:

```
/swe-init
```

**For best results: strip competing context.** The commands are the process. Every other source of system-level guidance — global rules files, skills, instructions in CLAUDE.md or AGENTS.md, tool descriptions telling the agent how to behave — competes with that process. The more the model has to reconcile conflicting instructions, the less reliably it follows any of them. Keep your global context files minimal. Let the commands do the talking.

---

## Templates

The `template/` directory contains starting points for:

- `README.md` — what the project does, how to build and test it, structure overview
- `CONVENTIONS.md` — questions that produce reasoning, not bare rules
- `DESIGN.md` — domain separation, system boundaries, tech rationale, data flows

These exist because the commands depend on finding honest, useful versions of these files in the projects they operate on.

---

## The philosophy in one sentence

Write it down before you write the code, scrutinize before you ship, and log what actually happened — because the next person in this codebase might be you, six months from now, wondering why.

---

## Inspiration

None of this is new. The ideas here are borrowed from people who thought harder about software than most of us ever will.

Kent Beck gave us TDD, and with it the insight that the order of operations matters morally, not just practically. RED → GREEN → BLUE is Beck's cycle with the BLUE phase made explicit — the moment where you stop trusting your own test and start attacking it.

Martin Fowler established refactoring as a discipline separate from feature work. `swe-refactor` exists because of that separation. Changing how code works and changing what it does are different acts; conflating them is where most projects quietly rot.

Fred Brooks warned that process matters more than heroics, and that you should plan to throw one away. The devlog.md is the antidote to heroics — it makes decisions visible so the next session doesn't have to reconstruct them from first principles.

Edsger Dijkstra showed that constraints are the source of reliability, not the enemy of it. Reading depth levels, frozen specs, and BLUE phase checks are all constraints. They feel slow. They are why this works.

Donald Knuth wrote programs to be read by humans and only incidentally executed by machines. The devlog.md per behavior, the BDD-phrased spec.md, the reasoning-first CONVENTIONS.md — all of it is literate programming by another name.
