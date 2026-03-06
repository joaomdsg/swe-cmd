# swe-cmd

Discipline is the source of speed, not the enemy of it.

## The problem nobody wants to name

Every AI coding tool in 2026 is obsessed with context engineering — curating what the model sees so it produces better output. Embeddings, RAG pipelines, dynamic rule loading, memory systems. All of it assumes the same thing: if the model sees the right information, it will do the right thing.

It won't.

A model with perfect context and no process will still:
- Implement what it inferred instead of what you meant
- Write tests that mirror the implementation instead of specifying behavior
- Leave no record of decisions, so every question gets re-litigated
- Drift from conventions without noticing, and without leaving a trail

Context engineering answers "what should the model see?" Discipline answers "what should the model *do* with what it sees?" The industry is solving the first question and ignoring the second.

## What this is

A methodology for agentic software development, encoded as executable commands. A set of habits — given shape so an agent can follow them and a human can audit them.

**Design for humans and the agent benefits for free.** Readable specs, honest conventions, and meaningful tests serve the human first. The agent inherits the clarity at no extra cost.

## Key ideas

**Reading depth prevents blind modifications.** Three levels: Interface → Logic → History. One hard rule: never modify a file you've only read at Depth 1. Simple constraint, prevents one of the most common agent failures.

**Conventions are questions, not rules.** Every CLAUDE.md and AGENTS.md in the wild is a list of bare directives with no reasoning. swe-cmd's CONVENTIONS.md template forces you to answer *why* — "Where have errors bitten you before?" "What makes a test brittle in this project?" A rule without a why is fragile.

**The BLUE phase catches what RED→GREEN misses.** TDD's standard loop is RED → GREEN → refactor. swe-cmd extends it: RED → GREEN → BLUE → REVIEW. BLUE is where you try to break your own test: mutation check, false pass check, behavior vs. implementation check. Tests that pass for the wrong reason are worse than no tests.

**The devlog.md is memory that doesn't evaporate.** Chat history disappears. swe-cmd writes a devlog.md entry per behavior, per phase — with commit hashes, decisions made, conventions diverged from, and blind spots flagged. Start a new session six months later, read the devlog.md, know exactly where things stand.

## The honest gap

swe-cmd optimizes the *process layer* — what the agent does and in what order. It doesn't optimize the *information layer* — what the agent sees at each step.

The strongest version would dynamically load the right spec section, devlog entry, and conventions based on which behavior is being implemented. That's not built yet. Right now it's plain markdown files and a disciplined agent. That still beats a context-optimized agent with no discipline.

## Commands

| Command | Purpose |
|---|---|
| `/swe-init` | Phase Zero — orient, read the project, establish conventions, create `.swe/` |
| `/swe-spec` | Interview → behaviors → frozen spec.md |
| `/swe-respec` | Supersede or deprecate an existing spec.md |
| `/swe-implement <feature>` | Micro TDD cycle per behavior |
| `/swe-fix <description>` | Fix a bug with TDD — no spec.md, test IS the spec |
| `/swe-refactor <description>` | Refactor without altering behavior — existing tests are the safety net |
| `/swe-verify` | spec.md↔test coverage audit |
| `/swe-status` | Standalone health audit, run anytime |

## What gets created in your project

Running `/swe-init` creates a `.swe/` directory:

```
.swe/
  map.md
  spec/
    feat-<behavior-centric-name>/
      spec.md       # BDD behaviors. Frozen when approved.
      devlog.md     # Decisions, blocks, overrides, divergence.
  _archive/
    superseded/
    deprecated/
```

## Typical workflow

```
/swe-init           # once per project
/swe-spec           # interview → behaviors → frozen spec.md
/swe-implement      # TDD cycle per behavior (RED → GREEN → BLUE → REVIEW)
/swe-verify         # spec.md↔test coverage audit before shipping
/swe-status         # run anytime to catch drift
```

## Getting started

Requires [OpenCode](https://opencode.ai) on your PATH (also picks up Claude Code and Cursor context files).

```sh
git clone https://github.com/joaomdsg/swe-cmd.git
ln -s /path/to/swe-cmd/commands ~/.config/opencode/commands
```

Then in any project: `/swe-init`

Keep your global context files minimal — competing instructions dilute the process.

## Templates

The `template/` directory contains starting points for:

- `README.md` — what the project does, how to build and test it, structure overview
- `CONVENTIONS.md` — questions that produce reasoning, not bare rules
- `DESIGN.md` — domain separation, system boundaries, tech rationale, data flows

These exist because the commands depend on finding honest, useful versions of these files in the projects they operate on.

## The philosophy in one sentence

Write it down before you write the code, scrutinize before you ship, and log what actually happened — because the next person in this codebase might be you, six months from now, wondering why.

## Inspiration

- **Kent Beck** — TDD, and the insight that order of operations matters. BLUE is Beck's cycle with the attack phase made explicit.
- **Martin Fowler** — refactoring as a discipline separate from feature work. Conflating them is where most projects quietly rot.
- **Fred Brooks** — process matters more than heroics. The devlog.md makes decisions visible so the next session doesn't reconstruct them from first principles.
- **Edsger Dijkstra** — constraints are the source of reliability. Reading depth levels, frozen specs, BLUE phase checks — they feel slow; they are why this works.
- **Donald Knuth** — programs written for humans, only incidentally executed by machines. The devlog.md, BDD spec.md, reasoning-first CONVENTIONS.md — literate programming by another name.
