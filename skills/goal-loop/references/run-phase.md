# Run Phase — Quality-Convergence Loop Playbook

The Run phase is a feedback loop that does not stop until every criterion in `GOAL.md`
verifiably passes and no available verification surface reports an issue. There is no
iteration cap, no token budget, and no "close enough." The loop ends when the evidence
says it ends — not when the work feels done.

---

## Workspace Isolation (PRECONDITION — before iteration 1)

The loop commits repeatedly and autonomously. Before the first iteration begins, confirm
that work is on an **isolated feature branch or git worktree** — never on `main` or
`master`.

**If the current branch is `main` or `master`:**

1. **Preferred path:** Use `superpowers:using-git-worktrees` to create a worktree or
   switch to an isolated branch. Do not reimplement worktree or branch logic here — that
   skill owns it.
2. **Consent path:** If the user has explicitly stated (in the current session) that they
   want the loop to run on `main`/`master`, you may proceed. Implicit acceptance is not
   consent — proceed only on an unambiguous statement.

Do **not** start the iteration loop while on `main`/`master` without one of the two
conditions above being satisfied.

---

## Verification Surfaces

Two surfaces are always in scope. Run both on every iteration. A surface is **skipped**
only when it physically cannot exercise the change (e.g., a CLI-only change has no
browser surface to start). A surface that _could_ exercise the change but is merely
inconvenient is **not skippable**. If a GOAL.md criterion names a browser observable
(console errors, network requests, screenshots, interactions), the browser surface is
non-negotiable — green unit tests do not satisfy a browser criterion.

### Surface A — Static / Test

Run every static check the project has: tests, type checking, linting. The specific
commands are the ones named in `GOAL.md`; fall back to project-standard commands
(`npm test`, `npx tsc --noEmit`, `npx eslint src/`) when a criterion doesn't specify.

Every failure here is a verifiable defect. On failure, reach for existing skills:

- **test-driven-development** — write a failing test that reproduces the defect, then
  make it pass. Do not fix code in the dark.
- **systematic-debugging** — when the cause is unclear, run the debugging playbook:
  isolate, hypothesize, verify, fix.

Do not move to convergence passes while any test, type error, or lint error is open.

### Surface B — Runtime / Browser

When the change touches anything that renders in a browser or runs in a dev server,
exercise it with the `preview_*` tools in this order:

1. `preview_start` — bring the dev server up (or reload it if already running).
2. `preview_console_logs` and `preview_network` — scan for errors and failed requests
   before anything else. A console error is a verifiable defect, not a warning.
3. `preview_snapshot` — capture the DOM structure to verify the expected elements exist.
4. `preview_screenshot` — visual verification; compare to any baseline named in GOAL.md.
5. `preview_click` / `preview_fill` — exercise interactions the criterion specifies.

Any console error, failed network request, broken interaction, or structural mismatch
keeps the loop running. Do not declare success from a screenshot alone — check the logs
first.

**The browser surface closes a gap unit tests cannot close.** A unit test that mocks
a module will not catch a runtime misconfiguration, a missing asset, or a timing error
in actual browser execution. Run both surfaces; they are complementary, not redundant.

---

## Iteration Logic

Each iteration follows this sequence exactly. Do not reorder it.

1. **Re-verify GOAL.md criteria.** Run each named check (command, test, browser
   observable). Use the **verification-before-completion** skill — produce evidence (tool
   output, screenshots, test results), never bare assertions. If a criterion says
   "verified via `preview_console_logs`," open the browser surface and read the logs;
   "unit tests pass" is not a substitute.

2. **Decision point:**
   - If every criterion passes **and** no surface reports an issue → proceed to
     **convergence passes** (see below).
   - If any criterion fails or any surface reports an issue → proceed to **fix**.

3. **Fix.** Pick the highest-priority open criterion or surface issue. Fix it using
   existing skills (TDD for behavioral failures, systematic-debugging for unclear
   causes). Commit when the fix is self-contained. Return to Step 1.

The loop is a cycle, not a checklist. You may pass through it dozens of times.
Each pass is complete — do not abbreviate verification because "nothing else changed."

---

## Convergence Passes (run once, before declaring done)

When Step 1 shows everything passing, do not declare success yet. Run these four passes
in order, then do one final efficient re-verification sweep.

### Pass 1 — Dead-Code Cleanup

Scan for code that **the loop itself made unused** (helpers extracted and replaced,
branches eliminated by a fix, imports no longer referenced after a refactor). Remove
those orphans. They are your mess; clean them up.

**Pre-existing dead code is a different matter entirely.** Code that was already unused
before the loop began is **flagged, not deleted**. Add a comment such as:

```
// LOOP-FLAG: appears unused prior to this goal; not deleted per run-phase rules.
// GOAL.md would need to authorize removal.
```

Do not delete pre-existing dead code even if it is "obviously" unused. You cannot be
certain it was not intentionally retained (test fixture, future seam, conditional
platform). The only exception: `GOAL.md` explicitly authorizes cleanup of pre-existing
dead code by name or scope. If it does, treat it the same as loop-created orphans.

This rule is surgical-changes applied to the cleanup pass. Your loop is accountable for
what it created, not for what came before it.

### Pass 2 — Simplify / Reuse

Run `/simplify` and `/code-review` against the code the loop touched. If either skill is not available in the current environment, perform an equivalent manual reuse-and-simplification pass instead. These existing skills know what to look for; do not reimplement their logic here. Apply the findings
that are within the declared scope of `GOAL.md`. Decline any finding that would change
behavior not covered by a GOAL.md criterion — note the finding in LOOP-LOG instead.

The goal of this pass is efficiency: cut over-engineering, prefer reuse over duplication,
match existing patterns in the codebase rather than inventing new ones.

### Pass 3 — Append LOOP-LOG

Open (or create) `LOOP-LOG.md` in the project root (or directory specified in GOAL.md)
and append an entry using `assets/LOOP-LOG.template.md`. Record:

- **Attempted:** what changed in this iteration set.
- **Verified:** which criteria and surfaces were checked, and what they returned.
- **Remaining:** any open items, flags, or out-of-scope findings.

LOOP-LOG is append-only. Do not edit prior entries. It is the audit trail that makes
unattended runs inspectable and resumable after an interruption.

### Pass 4 — Efficient Re-Verification

Run verification again, but limit it to what the convergence passes could have affected.
If Pass 1 removed an import, re-run the type check. If Pass 2 touched a component,
re-run its tests and reload the browser surface. Do not re-run the full suite if only
a comment changed — use judgment about what is actually at risk.

If Pass 4 finds a new issue, the convergence passes introduced a regression. Do not
declare done. Return to the fix step, resolve it, and run the convergence passes again.

If Pass 4 is clean, all three Stop Condition requirements are met — conditions 1 and 2 were verified in Step 1, condition 3 is now verified. Declare done and report.

---

## Coding Discipline (always active, every iteration)

These apply throughout the loop, not just in the convergence passes.

**Reuse before writing.** Before adding a new helper, function, or component, search
the codebase (via an Explore subagent or Grep) for something that already does the job.
Duplication in a convergence loop compounds — code written in iteration 3 may solve the
same problem the loop already solved differently in iteration 1.

**Surgical changes.** Change what is broken; leave everything else alone. Do not
improve adjacent code, fix style in files you're touching, or refactor outside the scope
of the current criterion. If you notice something worth fixing that is outside scope,
add it to LOOP-LOG under "Remaining" — do not act on it.

**YAGNI.** No features, abstractions, or configurability that `GOAL.md` does not
require. Every line of code you add is a line the loop has to maintain, test, and verify.
Keep the diff small.

---

## Stop Condition

The loop stops when and only when:

1. Every criterion in `GOAL.md` passes its named check (evidence on file, not an
   assertion).
2. No verification surface — static or runtime — reports an issue for this change.
3. The convergence passes are clean (Pass 4 re-verification found nothing).

There is no iteration cap, no time limit, and no "good enough." If the loop is still
running after many iterations, that is information: the goal is harder than expected,
or a criterion is blocking progress for a structural reason. Escalate to the user with
a concrete description of what is stuck — do not declare partial success.

When all three conditions are met, report success with:
- The evidence for each GOAL.md criterion (outputs, screenshots, test results).
- A summary of what changed across the loop (loop iterations, commits made).
- Any flags left in LOOP-LOG (pre-existing dead code, out-of-scope findings).
