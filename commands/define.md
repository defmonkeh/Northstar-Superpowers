---
description: Define an airtight, falsifiable goal (adversarial, hard-gated) before any loop.
---

Invoke the `northstar-superpowers:goal-loop` skill, entering the **Define** phase.
Interrogate the goal one challenge at a time and do not proceed to the convergence
loop until `GOAL.md` passes the hard gate (every criterion measurable, falsifiable,
and red-teamed).

When the gate passes, also emit a single paste-ready completion condition so the goal
can be handed to a persistence engine (e.g. Claude Code's built-in `/goal`) to drive
the loop across turns. See the skill's `references/define-phase.md` for the pairing.
