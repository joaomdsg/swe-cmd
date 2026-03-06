# Design

Structural reasoning for this system. The goal is to make decisions traceable — not just what was chosen, but why, and what was rejected.

---

## Domain Separation

What are the domains in this system? Where are the boundaries, and why are they there?

_What belongs together, what must stay apart, and what enforced that decision?_

---

## System Boundaries

What is internal to this system? What is external?

- **Internal:** things this system owns and controls
- **External:** things this system calls, depends on, or integrates with
- **Edges:** where internal meets external — where trust changes, where errors surface, where schema mismatches happen

_What are the integration points, and what contracts do they rely on?_

---

## Tech Stack

### Language / Runtime

_Why this language? What was considered and rejected?_

### Framework / Libraries

_Why this framework? What problem does each key library solve? What was the alternative?_

### Infrastructure

_Where does this run? Why? What's the deployment model?_

---

## Data Flows

How does data move through the system?

- Where does it enter?
- Where is it validated or transformed?
- Where is it stored?
- Where does it exit?

_A diagram or a prose walkthrough — whichever is clearer. Focus on the path that matters most._

---

## Key Tradeoffs

Decisions worth recording because they had real alternatives:

| Decision | Alternatives considered | Reasoning |
|---|---|---|
| | | |

_Add entries as decisions are made. The goal is a paper trail, not a complete record from day one._

---

## What We'd Do Differently

_Honest retrospective on decisions that turned out to be wrong, expensive, or limiting. Update as you learn._

-
