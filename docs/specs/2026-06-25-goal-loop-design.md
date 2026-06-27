# Northstar Superpowers — `goal-loop` Design

**Date:** 2026-06-25
**Status:** Design (awaiting review)
**Repo:** `northstar-superpowers` (public, MIT)
**Upstream:** Fork of [obra/superpowers](https://github.com/obra/superpowers) (MIT, © 2025 Jesse Vincent)

---

## 1. Purpose

Northstar Superpowers is a fork of the `superpowers` skill set whose distinctive
addition is a single combined skill, **`goal-loop`**, that helps **create better
products** by:

1. Pinning an **airtight, falsifiable goal** through a stronger-than-brainstorming
   adversarial interrogation, then
2. Driving toward it with an **autonomous quality-convergence loop** that does not
   stop while any available tool can prove something is broken.

The thesis: **rigor up front earns autonomy later.** A vague or gameable goal makes
an unattended loop optimize the wrong thing and "succeed" on paper. An airtight goal
makes the loop safe to run.

## 2. Naming, licensing, attribution

- Set renamed **Northstar Superpowers**; plugin manifest `name` → `northstar-superpowers`.
- Public repo. **MIT preserved**: keep the original `LICENSE` (© 2025 Jesse Vincent)
  and add the fork's own copyright line under it.
- `README` and plugin `description` state plainly: *"Forked from obra/superpowers."*
- A `NOTICE`/attribution line links back to the upstream repo.

## 3. Shape & entry

One combined skill with two phases separated by a **hard gate**:

```
/goal ──▶ Define phase ──[hard gate: airtight GOAL.md]──▶ Run phase ──▶ stop
/loop ──▶ (attempts Run; gate falls back to Define if no airtight GOAL.md) ┘
```

- **Two triggers, one body.** `/goal` enters at Define. `/loop` attempts Run but,
  because of the hard gate, falls back to Define when no vetted `GOAL.md` exists.
- This keeps the original `/goal` + `/loop` vocabulary while guaranteeing the loop
  can never run without a vetted goal.

## 4. The `GOAL.md` artifact

A standalone file the whole flow references. Sections:

- **North-star** — one sentence naming the real outcome (not the task).
- **Success criteria** — a checklist of **falsifiable, machine-verifiable** assertions.
  Each criterion **must name how it is checked** (a command, a test, an observable in
  the browser/dev server).
- **Standing criterion (implicit, every goal inherits it):** *zero verifiable defects
  across all available verification surfaces.*
- **Non-goals / out-of-scope** — what the loop must not wander into.
- **Done-definition** — the loop stops when, and only when, every criterion verifiably
  passes **and** no verification surface reports an issue.

## 5. Define phase — adversarial, hard-gated

Stronger than standard brainstorming. One challenge at a time:

- **Problem-behind-the-problem** — why this, why now, what is the unstated intent
  (5-whys).
- **Red-team every criterion** — "how could the loop mark this done while missing the
  point?" (Goodhart check). Any gameable or unmeasurable criterion is rewritten or
  rejected.
- **Pushback on premise, scope, feasibility** — not just "what do you want" but "what
  are you assuming, what breaks this, is this the right goal."

**Gate (no override):** refuses to write `GOAL.md` or begin Run until *every* success
criterion is **measurable, falsifiable, and red-teamed**.

## 6. Run phase — quality-convergence loop

**Workspace isolation (precondition, before iteration 1):** The loop commits repeatedly
and autonomously, so it must run on an isolated feature branch or git worktree — never on
`main`/`master` without explicit user consent. If the current branch is `main`/`master`,
the loop creates/switches to an isolated branch (via `superpowers:using-git-worktrees`)
or, only with the user's unambiguous consent, proceeds on `main`. Implicit acceptance is
not consent.

Each iteration runs a **full verification sweep** and stops only when all surfaces are
clean.

### 6.1 Verification surfaces

- **Static / test** — tests pass, types check, lint clean.
  - On failure → **test-driven-development** + **systematic-debugging**.
- **Runtime / browser** — when the change is previewable, use the `preview_*` tooling
  (never a manual "please check" handoff):
  `preview_start` → reload → `preview_console_logs` / `preview_network` for errors →
  `preview_snapshot` for structure → `preview_screenshot` / `preview_click` /
  `preview_fill` for interactions.
  - Any console error, failed request, or broken interaction is a **verifiable code
    issue** and keeps the loop running.
- A surface that **cannot exercise** the change is **skipped, not failed**.

### 6.2 Iteration

1. Re-verify `GOAL.md` criteria with their named checks
   (reuses **verification-before-completion** — evidence, never assertions).
2. If every criterion passes **and** no surface reports an issue → run the convergence
   passes (§6.3); if those are clean too → **stop, report success**.
3. Otherwise → pick the next unmet criterion / open issue, fix it using the existing
   skills (TDD, systematic-debugging), commit, repeat.

**Stop condition:** *goal met, and nothing left that any available tool can prove is
broken.* No iteration or token caps in the skill itself (per design choice); the
runtime's own backstops and the user's ability to interrupt remain.

### 6.3 Convergence passes (before declaring done)

- **Dead-code / cleanup** — remove orphans the loop **itself created**; **flag, do not
  delete**, pre-existing dead code unless the goal explicitly authorizes it. (Honors
  the surgical-changes rule: never delete working code the loop didn't make unused.)
- **Simplify / reuse** — run the existing `/simplify` and `/code-review` skills to cut
  over-engineering and prefer reuse. The "efficient coding" lever.
- **Loop journal** — append-only state file (`LOOP-LOG.md`) recording each iteration:
  attempted / verified / remaining. Provides **resumability** and an **audit trail**
  for unattended runs.
- **Efficient re-verification** — within a run, only re-verify what the last change
  could affect rather than re-running every check from scratch. Saves time/tokens; not
  a stop-cap.

## 7. Coding-efficiency discipline (baked into Run)

- **Reuse before writing** — search for an existing helper (Explore subagent) before
  adding new code.
- **Surgical changes** — don't improve adjacent code, match existing style, don't
  refactor what isn't broken.
- **YAGNI** — no features, abstractions, or flexibility beyond what `GOAL.md` requires.

## 8. Router wiring

`using-superpowers` adds `goal-loop` to its flowchart so the model reaches for it when
a task is "define a measurable outcome and drive toward it autonomously." The existing
workflow skills are unchanged; `goal-loop` orchestrates over them rather than
reimplementing execution.

## 9. What is reused vs. new

| Concern | Source |
|---|---|
| Adversarial Define phase | **New** (stronger variant of brainstorming) |
| `GOAL.md` artifact | **New** |
| Autonomous convergence loop | **New** orchestration layer |
| Loop journal, cleanup, efficient re-verify | **New** |
| Execution inside iterations | Reused: test-driven-development, systematic-debugging |
| Verification | Reused: verification-before-completion + `preview_*` tools |
| Simplify / review pass | Reused: `/simplify`, `/code-review` |
| Discoverability | Edit: `using-superpowers` router |

## 10. Out of scope (this spec)

- Repo scaffolding mechanics (copying upstream files, manifest edits, marketplace
  registration) — handled in the implementation plan.
- Iteration/budget stop-caps — explicitly excluded by design choice.
- Deep edits to brainstorming/writing-plans/execution skills — `goal-loop` wraps them,
  it does not modify them.
