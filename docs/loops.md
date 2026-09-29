# Autonomous loops

Where the commands above are one-shots, the loops keep durable state in configurable operational
directories. Each loop defines its own terminal outcomes, records real verification evidence, preserves
pre-existing work, and tears down OMC persistence on exit. They use tools and subagents directly rather
than trying to invoke other slash commands.

Install these as **skills** (directories under `~/.claude/skills/`), not flat commands. Each includes its
own templates and may include supporting references.

## `/goal-loop` — drive a repository toward an explicit goal

Saves the user's goal verbatim, then runs an inspect/plan → implement → verify → fresh-review cycle over
the smallest unfinished slice. State defaults to `.goal-loop/`; iteration, no-progress, and failure limits
prevent runaway work. Routine reversible edits can proceed, while merge, release, deployment, production
mutation, destructive history changes, and unrequested external effects require clear authorization.

*Use when:* a repository should make sustained, evidence-backed progress toward a concrete outcome.
`/goal-loop <goal>`

## `/refine-loop` — polish a finished app until it stops paying off

Audits a working repository through architecture, usability, production-readiness, and refactoring lenses.
It ranks behavior-preserving candidates by `Impact × Confidence ÷ Effort`, applies one small change at a
time, verifies independently, and stops after three evidence-based plateau rounds or a safety limit.
Correctness, security, privacy, and data-loss findings become explicit `NEEDS-FIX` handoffs rather than
being silently mixed into refinement. State defaults to `.refine-loop/`.

*Use when:* existing behavior works and should be improved without changing its contract.
`/refine-loop [--target=PATH] [theta=N] [focus=LENS,...] <intent>`

## `/docs-loop` — bring many repos' docs in sync with the code

Audits a finite set of repositories against each repository's queried default branch. Dry-run is the
default: it reports affirmative contradictions and defers uncertain claims without editing. `--apply`
authorizes isolated local documentation fixes; adding `--open-prs` authorizes normal commits, pushes, and
one reviewable PR per repository. It never merges and never treats a missing search result as proof.

*Use when:* documentation needs an evidence-backed drift report or surgical updates.
`/docs-loop [--apply [--open-prs]] [--root=PATH | REPO_PATH ...]`

> **Autonomy versus resumption:** OMC provides unattended continuation. Without OMC, each loop performs
> the current authorized pass and reports the exact manual resume action. Mutating modes persist state;
> `docs-loop` dry-run remains read-only and emits a non-resumable report.
