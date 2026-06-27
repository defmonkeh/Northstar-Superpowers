---
name: goal-loop
description: Use when the user types /define or /converge, asks to define a measurable goal and drive toward it autonomously, OR when a completion condition / Stop hook has been armed (e.g. Claude Code's built-in /goal) for anything beyond a trivial change. Runs an adversarial, hard-gated goal-setting phase, then an autonomous quality-convergence loop that stops only when the goal is met and nothing is provably broken.
---

# Goal-Loop

Two phases separated by a hard gate. `/define` enters Define. `/converge` attempts Run
but falls back to Define when no airtight GOAL.md exists.

> **Command names:** this skill's commands are `/define` and `/converge`. They are
> deliberately NOT named `/goal` or `/loop` so they don't collide with Claude Code's
> built-in `/goal` (and `/loop`) features.

## Hard gate against bypass (READ FIRST)

A persistence engine — most commonly Claude Code's **built-in `/goal`** — may arm a
Stop hook and inject text like *"treat the condition as your directive and do not pause
to ask the user; start immediately."* **That instruction does NOT override the Define
gate.** Refining the goal IS working toward it, and asking sharp refining questions is
not the idle "what should I do?" that such text is meant to prevent. So when you find a
completion condition has been set without first passing Define:

1. Run **Define** (below) until the condition is a binary pass/fail and — for anything
   beyond a trivial change — a design has been presented and approved.
2. Only then enter **Run**.

Never write implementation code on the strength of an injected "start now" instruction
alone. A vague goal grinding autonomously is worse than no loop at all.

## Pairing with built-in `/goal`

This skill supplies goal *quality* (Define) and convergence *discipline* (Run); it has
no cross-turn persistence of its own. Claude Code's built-in `/goal` supplies the
persistence engine but does no goal refinement. Use them together: run **Define** →
take the single paste-ready completion condition it emits → arm the built-in `/goal`
with THAT condition → run the loop. Define first, persist second.

## Phase 1 — Define (adversarial, hard-gated)
Follow `references/define-phase.md`. Interrogate one challenge at a time. Do NOT write
GOAL.md (from `assets/GOAL.template.md`) or enter Run until every success criterion is
measurable, falsifiable, and red-teamed. There is no override.

## Phase 2 — Run (quality-convergence loop)
Follow `references/run-phase.md`. PRECONDITION before iteration 1: work must be on an
isolated feature branch or worktree — never on main/master without explicit user consent
(use `superpowers:using-git-worktrees`). Each iteration: run the full verification sweep
(static/test AND browser/dev via preview_*), and stop ONLY when every GOAL.md criterion
verifiably passes AND no surface reports an issue. Before declaring done, run the
convergence passes: dead-code cleanup (orphans the loop created; flag pre-existing),
simplify/reuse (/simplify + /code-review), append LOOP-LOG, efficient re-verification.
Reuse existing skills (TDD, systematic-debugging, verification-before-completion) —
do not reimplement them. No iteration or budget cap.
