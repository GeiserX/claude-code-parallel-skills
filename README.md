# Claude Code Parallel Skills

Reusable workflows for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) in two families:

1. **Parallel commands** — spawn many specialized agents *at once* to review, research, investigate, and implement faster and more thoroughly.
2. **Durable loops** — preserve progress outside model context, resume across sessions, and stop on explicit evidence.

The parallel commands work with Claude Code's Agent tool and can use
[oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode) (OMC) specialists when available.
The loops require OMC only for unattended continuation; without it, their saved state remains manually
resumable.

---

## Parallel commands

One-shot commands that fan out specialized agents in parallel, then synthesize.

### `/investigate!`

Root-cause analysis and fix. Spawns 7+ agents (tracer, scientist, architect, security reviewer, critic, test engineer, debugger) to investigate an issue simultaneously, synthesizes findings, then implements a fix with a reproduction test.

### `/review-pr!`

Multi-perspective PR review. Spawns 7+ simultaneous reviewers (logic, architecture, security, simplicity,
tests, performance, data integrity), then synthesizes into a severity-ranked verdict with a clear
APPROVE / REQUEST CHANGES / NEEDS DISCUSSION outcome.

### `/review-code!`

Whole-codebase health audit. Spawns 7+ auditors (architecture, security, complexity, dead code, error handling, test health, consistency) that examine the entire repo through orthogonal lenses, then synthesizes into a scored health report with prioritized actions.

### `/research!`

Deep parallel research on any topic. Spawns 7+ researchers (docs specialist, codebase explorer, git historian, comparativist, architect, critic, performance analyst) that attack the question from different angles, then synthesizes into actionable findings with options and trade-offs.

### `/implement!`

Parallel feature implementation. Decomposes work into independent streams, scaffolds contracts/interfaces first, then dispatches N executor agents working on non-overlapping files simultaneously. Finishes with parallel verification (code review + tests + architecture check).

---

## Review automation

### `/coderabbit-loop`

A bounded [CodeRabbit](https://coderabbit.ai) review-and-fix loop. It verifies each finding against the
current code, applies only valid minimal fixes, runs repository checks, pushes normally, and waits for a
review tied to the new head SHA. It stops at `READY` by default. Merge, release, and deployment are separate
explicit options with their own safety gates.

---

## Autonomous loops

Where the commands above are one-shots, the loops keep durable state in configurable operational
directories. Each loop defines its own terminal outcomes, records real verification evidence, preserves
pre-existing work, and tears down OMC persistence on exit. They use tools and subagents directly rather
than trying to invoke other slash commands.

Install these as **skills** (directories under `~/.claude/skills/`), not flat commands. Each includes its
own templates and may include supporting references.

### `/goal-loop` — drive a repository toward an explicit goal

Saves the user's goal verbatim, then runs an inspect/plan → implement → verify → fresh-review cycle over
the smallest unfinished slice. State defaults to `.goal-loop/`; iteration, no-progress, and failure limits
prevent runaway work. Routine reversible edits can proceed, while merge, release, deployment, production
mutation, destructive history changes, and unrequested external effects require clear authorization.

*Use when:* a repository should make sustained, evidence-backed progress toward a concrete outcome.
`/goal-loop <goal>`

### `/refine-loop` — polish a finished app until it stops paying off

Audits a working repository through architecture, usability, production-readiness, and refactoring lenses.
It ranks behavior-preserving candidates by `Impact × Confidence ÷ Effort`, applies one small change at a
time, verifies independently, and stops after three evidence-based plateau rounds or a safety limit.
Correctness, security, privacy, and data-loss findings become explicit `NEEDS-FIX` handoffs rather than
being silently mixed into refinement. State defaults to `.refine-loop/`.

*Use when:* existing behavior works and should be improved without changing its contract.
`/refine-loop [--target=PATH] [theta=N] [focus=LENS,...] <intent>`

### `/docs-loop` — bring many repos' docs in sync with the code

Audits a finite set of repositories against each repository's queried default branch. Dry-run is the
default: it reports affirmative contradictions and defers uncertain claims without editing. `--apply`
authorizes isolated local documentation fixes; adding `--open-prs` authorizes normal commits, pushes, and
one reviewable PR per repository. It never merges and never treats a missing search result as proof.

*Use when:* documentation needs an evidence-backed drift report or surgical updates.
`/docs-loop [--apply [--open-prs]] [--root=PATH | REPO_PATH ...]`

> **Autonomy versus resumption:** OMC provides unattended continuation. Without OMC, each loop performs
> the current authorized pass and reports the exact manual resume action. Mutating modes persist state;
> `docs-loop` dry-run remains read-only and emits a non-resumable report.

---

## Installation

**Parallel commands** → your commands directory:

```bash
mkdir -p ~/.claude/commands
cp skills/*.md ~/.claude/commands/          # user-level (all projects)
# or, per-project:
mkdir -p .claude/commands && cp skills/*.md .claude/commands/
```

**Autonomous loops** → your skills directory (copy the whole directory, templates and references included):

```bash
mkdir -p ~/.claude/skills
cp -R loops/goal-loop loops/refine-loop loops/docs-loop ~/.claude/skills/
```

## Usage

```text
/investigate! Users getting 500 errors on /api/checkout
/review-pr!
/review-code!
/research! How does connection pooling work in our app?
/implement! Add webhook retry with exponential backoff
/coderabbit-loop

/goal-loop Ship OAuth device-flow login and get CI green
/refine-loop --target=. theta=1.5 focus=usability make the CLI genuinely pleasant to use
/docs-loop --root=~/src
/docs-loop --apply --open-prs ~/src/project-a ~/src/project-b
```

Each command accepts `$ARGUMENTS`. Mutating and outward-facing loop options are intentionally explicit;
read each loop's authorization section before invocation.

## Design Philosophy

These skills exploit three well-documented principles:

**Cognitive diversity beats individual depth.** 3-4 distinct perspectives catch 80-90% of defects — diversity matters more than reviewer count (Fagan, IBM 1976). Independent agents with constrained viewpoints outperform a single generalist, the same mechanism behind random forests in ML and the Delphi method in expert forecasting (Surowiecki, "The Wisdom of Crowds").

**Parallel hypothesis testing beats serial investigation.** Enumerating all candidate causes before pursuing any prevents premature convergence (NASA Fault Tree Analysis). Structured post-mortems with parallel tracks reduce repeat incidents by 40-60% (Beyer et al., "Site Reliability Engineering", 2016). A single investigator cannot reliably falsify their own theory due to confirmation bias (Kahneman, "Thinking Fast and Slow").

**Communication overhead must be designed out, not managed.** Communication scales O(n²) with team size (Brooks, "The Mythical Man-Month"). The countermeasure: define interfaces before parallelizing so agents share contracts, not conversations. Interface-first decomposition yields 3-5x throughput gains in teams >4 (Forsgren et al., "Accelerate", 2018).

The **loops** add a fourth: **durable state beats memory.** Goals, progress, counters, and stop signals live
in operational state files rather than only in model context, so work can resume instead of restarting and
stop on recorded evidence rather than a feeling.

<details>
<summary>Full references</summary>

- Agans, D. (2002). *Debugging: The 9 Indispensable Rules for Finding Even the Most Elusive Software and Hardware Problems*. AMACOM.
- Bacchelli, A. & Bird, C. (2013). "Expectations, Outcomes, and Challenges of Modern Code Review." *ICSE 2013*. (Microsoft; n=900+ reviews)
- Beyer, B. et al. (2016). *Site Reliability Engineering*. O'Reilly.
- Brooks, F. (1975). *The Mythical Man-Month*. Addison-Wesley.
- Cataldo, M. et al. (2006). "Identification of Coordination Requirements." *IEEE TSE*.
- Cockburn, A. (2004). *Crystal Clear*. Addison-Wesley. (Walking Skeleton pattern)
- De Bono, E. (1985). *Six Thinking Hats*. Little, Brown and Company.
- Fagan, M. (1976). "Design and Code Inspections." *IBM Systems Journal*.
- Forsgren, N. et al. (2018). *Accelerate*. IT Revolution Press.
- Gawande, A. (2009). *The Checklist Manifesto*. Metropolitan Books.
- Kahneman, D. (2011). *Thinking, Fast and Slow*. Farrar, Straus and Giroux.
- Martin, R.C. (2017). *Clean Architecture*. Prentice Hall.
- McConnell, S. (2004). *Code Complete*. Microsoft Press.
- Ousterhout, J. (2018). *A Philosophy of Software Design*. Yaknyam Press.
- Winters, T. et al. (2020). *Software Engineering at Google*. O'Reilly.
- Zeller, A. (2009). *Why Programs Fail*. Morgan Kaufmann.

</details>

## Design Principles

- **Parallel by default** — all agents launch simultaneously, no sequential bottlenecks
- **Minimum floors, not ceilings** — non-trivial parallel commands define a baseline panel and scale up for
  larger tasks; their documented small-change and non-applicable-lens rules may scale down
- **Orthogonal lenses** — no two agents can produce the same finding; each owns one concern
- **Contracts before code** — `implement!` scaffolds interfaces first so parallel agents don't conflict
- **Evidence over opinion** — agents report confidence levels and cite sources/files/lines
- **Synthesize, don't aggregate** — cross-reference findings, flag contradictions, deduplicate
- **State in files, not heads** *(loops)* — goals, progress, and stop signals are durable and resumable
- **Stop cleanly** *(loops)* — halt on goal-met or diminishing returns; always tear down persistence

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- Git
- [GitHub CLI](https://cli.github.com/) with appropriate authentication for `/coderabbit-loop` and
  `/docs-loop --open-prs`
- [oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode) — optional specialist agents for the
  parallel commands and required only for unattended loop continuation

Repository-provided tests and linters remain authoritative. `/docs-loop --open-prs` requires `gitleaks`;
optional tools such as `lychee` and Mermaid parsers are used when already installed. The loops do not
silently install tools.

## License

[GPL-3.0](LICENSE)
