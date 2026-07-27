# Claude Code Parallel Skills

Slash-command skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) in two families:

1. **Parallel commands** — spawn many specialized agents *at once* to review, research, investigate, and implement faster and more thoroughly.
2. **Autonomous loops** — keep working hands-off toward a goal, resuming across sessions and stopping cleanly, built on top of those parallel commands.

They lean on [oh-my-claudecode](https://github.com/anthropics/oh-my-claudecode) (OMC) agent types and skills, but the parallel commands degrade gracefully to any Claude Code setup with the Agent tool.

---

## Parallel commands

One-shot commands that fan out specialized agents in parallel, then synthesize.

### `/investigate!`
Root-cause analysis and fix. Spawns 7+ agents (tracer, scientist, architect, security reviewer, critic, test engineer, debugger) to investigate an issue simultaneously, synthesizes findings, then implements a fix with a reproduction test.

### `/review-pr!`
Multi-perspective PR review. Spawns 7+ simultaneous reviewers (logic, architecture, security, simplicity, tests, performance), then synthesizes into a severity-ranked verdict with a clear APPROVE / REQUEST CHANGES / NEEDS DISCUSSION outcome.

### `/review-code!`
Whole-codebase health audit. Spawns 7+ auditors (architecture, security, complexity, dead code, error handling, test health, consistency) that examine the entire repo through orthogonal lenses, then synthesizes into a scored health report with prioritized actions.

### `/research!`
Deep parallel research on any topic. Spawns 7+ researchers (docs specialist, codebase explorer, git historian, comparativist, architect, critic, performance analyst) that attack the question from different angles, then synthesizes into actionable findings with options and trade-offs.

### `/implement!`
Parallel feature implementation. Decomposes work into independent streams, scaffolds contracts/interfaces first, then dispatches N executor agents working on non-overlapping files simultaneously. Finishes with parallel verification (code review + tests + architecture check).

### `/rabbit`
CodeRabbit wait-and-fix loop. Waits for [CodeRabbit](https://coderabbit.ai) to finish reviewing the current PR, addresses every issue it raised (fix → push → wait for re-review), and repeats until CodeRabbit approves with no remaining issues — then merges and cuts a release (semver), deploying if the repo has a deploy path. Turns "babysit the review bot" into one command.

---

## Autonomous loops

Where the commands above are one-shots, the **loops** keep going. Each one is an autonomous, **resumable**, file-state-backed driver: it writes its goal and progress to files in `docs/` so it survives context loss and compaction, keeps iterating hands-off ("the boulder never stops"), and **stops cleanly** the moment the goal holds or further work stops paying off. They orchestrate the parallel commands above inside an OMC persistent mode (`ralph` / `autopilot`), and always tear that state down (`cancel`) on exit.

Install these as **skills** (directories under `~/.claude/skills/`), not flat commands — each ships with its own `templates/` and `references/`.

### `/sergio-loop` — drive one repo to a goal
The single entry point for "go work on this until it's done." You give it a directive; it saves that **verbatim** to `docs/GOAL.md` (so the work is remembered), engages OMC persistent mode, then runs the core cycle **`/research! → /implement! → /review-pr!`** on repeat until the goal holds — logging evidence to `docs/AUTOPILOT-WORKLOG.md` and parking genuine human-only decisions in `docs/DEFERRED-QUESTIONS.md` instead of stopping to ask. Add `coordinate` to let two sessions work the same repo through a shared `docs/COORDINATE.md` board without clobbering each other. Optionally point it at a **knowledge base** (a decisions doc, wiki, or your own research command) so it resolves "what do we intend here?" before deferring.

*Use when:* you have a repo and a goal and want it driven hands-off. `/sergio-loop <your directive>`

### `/refine-loop` — polish a finished app until it stops paying off
The loop you run **after** a thing works. Not features, not diff-review — it takes a functionally-done app and makes it **genuinely usable, thoughtful, robust, and well-architected**, one behavior-preserving, externally-verified improvement at a time. It audits four lenses (architecture & code health, usability & thoughtfulness, production-readiness & operations, refactoring discipline), scores each candidate by `Impact × Confidence ÷ Effort` against a threshold **θ** (correctness/security must-fixes bypass the gate), executes the best with rollback-on-regression, records ADRs, and **halts on diminishing returns** — reporting "diminishing returns at θ; lower θ to go deeper." Never a big-bang rewrite. Writes `docs/REFINE-GOAL.md` + `docs/REFINE-BACKLOG.md` + `docs/REFINE-WORKLOG.md`.

*Use when:* "make it production-grade", "make it really usable", "polish/harden this". `/refine-loop [theta=N] <app + focus>`

### `/docs-loop` — bring many repos' docs in sync with the code
A repo-after-repo documentation sweep. Given a scope (repo names, a workspace phrase, or a path), it walks each repo in turn and fixes only the doc claims the **current code contradicts** — stale ports, flags, env vars, paths, endpoints, renamed symbols, dead links, broken Mermaid, missing coverage — making surgical, verified edits and opening **one PR per repo**. It is built to be safe first: every claim is checked against a `file:line` code anchor, it works in a throwaway `origin/main` worktree (never your dirty checkout), hand-authored prose / ADRs / `CLAUDE.md` / secrets are off-limits, and it never auto-merges. State lives in a central ledger so it resumes rather than restarts, and it halts on scope-covered or diminishing returns. It would rather leave a doc alone than invent a word.

*Use when:* "sync the docs with the code across my repos." `/docs-loop [theta=N] <scope>`

> **The loops need OMC.** They call OMC's `ralph` / `autopilot` / `cancel` for hands-off persistence and the `/research!` `/implement!` `/review-pr!` commands from this repo. On a plain Claude Code setup without OMC you'd need to supply an equivalent persistence mechanism (a Stop hook + a small state flag); the loop logic and file-state design still apply.

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
cp -R loops/sergio-loop loops/refine-loop loops/docs-loop ~/.claude/skills/
```

## Usage

```
/investigate! Users getting 500 errors on /api/checkout
/review-pr!
/review-code!
/research! How does connection pooling work in our app?
/implement! Add webhook retry with exponential backoff
/rabbit

/sergio-loop Ship OAuth device-flow login and get CI green
/refine-loop theta=1.5 make the CLI genuinely pleasant to use
/docs-loop all active repos
```

Each command accepts `$ARGUMENTS` — pass your topic, issue, feature, or directive right after it. The loops write their state into `docs/` in the target repo (and a central ledger, for `docs-loop`).

## Design Philosophy

These skills exploit three well-documented principles:

**Cognitive diversity beats individual depth.** 3-4 distinct perspectives catch 80-90% of defects — diversity matters more than reviewer count (Fagan, IBM 1976). Independent agents with constrained viewpoints outperform a single generalist, the same mechanism behind random forests in ML and the Delphi method in expert forecasting (Surowiecki, "The Wisdom of Crowds").

**Parallel hypothesis testing beats serial investigation.** Enumerating all candidate causes before pursuing any prevents premature convergence (NASA Fault Tree Analysis). Structured post-mortems with parallel tracks reduce repeat incidents by 40-60% (Beyer et al., "Site Reliability Engineering", 2016). A single investigator cannot reliably falsify their own theory due to confirmation bias (Kahneman, "Thinking Fast and Slow").

**Communication overhead must be designed out, not managed.** Communication scales O(n²) with team size (Brooks, "The Mythical Man-Month"). The countermeasure: define interfaces before parallelizing so agents share contracts, not conversations. Interface-first decomposition yields 3-5x throughput gains in teams >4 (Forsgren et al., "Accelerate", 2018).

The **loops** add a fourth: **durable state beats memory.** A loop's goal, progress, and stop-signal live in files (`GOAL.md`, a worklog, a scored backlog, a ledger) — never in the model's context — so the work survives compaction, resumes instead of restarting, and stops on evidence rather than a feeling.

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
- **Minimum floors, not ceilings** — each skill defines a minimum agent count (7) but scales up for larger tasks (10-15+)
- **Orthogonal lenses** — no two agents can produce the same finding; each owns one concern
- **Contracts before code** — `implement!` scaffolds interfaces first so parallel agents don't conflict
- **Evidence over opinion** — agents report confidence levels and cite sources/files/lines
- **Synthesize, don't aggregate** — cross-reference findings, flag contradictions, deduplicate
- **State in files, not heads** *(loops)* — goal/progress/stop-signal are durable and resumable
- **Stop cleanly** *(loops)* — halt on goal-met or diminishing returns; always tear down persistence

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- [oh-my-claudecode](https://github.com/anthropics/oh-my-claudecode) — for the specialized agent types (`tracer`, `architect`, `security-reviewer`, …) and, for the loops, the `ralph` / `autopilot` / `cancel` persistence skills.

Without OMC, the parallel commands still run via the generic Agent tool (results may vary); the loops expect OMC's persistent modes.

## License

[GPL-3.0](LICENSE)
