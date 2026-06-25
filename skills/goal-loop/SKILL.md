---
name: goal-loop
description: Use when the user types /goal or /loop, or asks to define a measurable goal and drive toward it autonomously. Runs an adversarial, hard-gated goal-setting phase, then an autonomous quality-convergence loop that stops only when the goal is met and nothing is provably broken.
---

# Goal-Loop

Two phases separated by a hard gate. `/goal` enters Define. `/loop` attempts Run but
falls back to Define when no airtight GOAL.md exists.

## Phase 1 — Define (adversarial, hard-gated)
Follow `references/define-phase.md`. Interrogate one challenge at a time. Do NOT write
GOAL.md (from `assets/GOAL.template.md`) or enter Run until every success criterion is
measurable, falsifiable, and red-teamed. There is no override.

## Phase 2 — Run
<added in Task 3>
