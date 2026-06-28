# Changelog — Northstar Superpowers

Fork-specific changes for **Northstar Superpowers**. For the upstream Superpowers
skill-library history, see [`RELEASE-NOTES.md`](./RELEASE-NOTES.md).

This project is a fork of [obra/superpowers](https://github.com/obra/superpowers) (MIT).
Northstar adds the **`goal-loop`** skill on top of the upstream skills.

## v1.0.0 — 2026-06-27 (pre-release)

First tagged release. The headline is the `goal-loop` skill and its commands.

### Added
- **`goal-loop` skill** — a two-phase, hard-gated workflow:
  - **Define** (`/define`): adversarial, one-question-at-a-time interrogation that
    refuses to proceed until every success criterion is measurable, falsifiable, and
    red-teamed (Goodhart check). Writes an airtight `GOAL.md`. No override.
  - **Converge** (`/converge`): autonomous quality-convergence loop — implement →
    verify (static/test **and** browser via `preview_*`) → check goal → repeat — that
    stops only when every criterion verifiably passes and nothing it touched is
    provably broken. Branch-isolation precondition; convergence passes (dead-code,
    simplify/reuse, LOOP-LOG). No iteration/budget cap by design.

### Changed
- **Commands renamed `/goal` → `/define` and `/loop` → `/converge`.** Claude Code shipped
  a built-in `/goal` (and `/loop`) that collided with — and shadowed — the skill's
  commands. The rename removes the clash. (Breaking for anyone who used the 0.1.0
  command names. The internal skill name `goal-loop` is unchanged.)

### Pairing with Claude Code's built-in `/goal`
The built-in `/goal` is a cross-turn **persistence engine** with no goal refinement;
`goal-loop` supplies the **refinement + convergence discipline**. They're complementary —
**define first, persist second:** run `/define`, take the paste-ready completion condition
it emits, arm the built-in `/goal` with it, then run. The Define gate also applies
defensively — an injected "start immediately / don't pause to ask" never overrides it.

### Known limitations
- `/plugin marketplace add` + `/plugin install` have **not** been smoke-tested against the
  live registry. This release is marked **pre-release** until that's verified.
