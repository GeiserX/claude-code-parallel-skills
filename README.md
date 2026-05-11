# Claude Code Parallel Skills

Five slash-command skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) that spawn multiple specialized agents in parallel to tackle complex tasks faster and more thoroughly.

These skills leverage [oh-my-claudecode](https://github.com/anthropics/oh-my-claudecode) agent types but work with any Claude Code setup that supports the Agent tool.

## Skills

### `/investigate!`

Root-cause analysis and fix. Spawns 7+ agents (tracer, scientist, architect, security reviewer, critic, test engineer, debugger) to investigate an issue simultaneously, synthesizes findings, then implements a fix with a reproduction test.

### `/review-pr!`

Multi-perspective PR review. Spawns 7+ simultaneous reviewers (logic, architecture, security, simplicity, tests, performance), then synthesizes findings into a severity-ranked verdict with a clear APPROVE / REQUEST CHANGES / NEEDS DISCUSSION outcome.

### `/review-code!`

Whole-codebase health audit. Spawns 7+ auditors (architecture, security, complexity, dead code, error handling, test health, consistency) that examine the entire repo through orthogonal lenses, then synthesizes into a scored health report with prioritized actions.

### `/research!`

Deep parallel research on any topic. Spawns 7+ researchers (docs specialist, codebase explorer, git historian, comparativist, architect, critic, performance analyst) that attack the question from different angles, then synthesizes into actionable findings with options and trade-offs.

### `/implement!`

Parallel feature implementation. Decomposes work into independent streams, scaffolds contracts/interfaces first, then dispatches N executor agents working on non-overlapping files simultaneously. Finishes with parallel verification (code review + tests + architecture check).

## Installation

Copy the skills into your Claude Code commands directory:

```bash
mkdir -p ~/.claude/commands
cp skills/*.md ~/.claude/commands/
```

Or, to install into a specific project:

```bash
mkdir -p .claude/commands
cp skills/*.md .claude/commands/
```

## Usage

From any Claude Code session:

```
/investigate! Users getting 500 errors on /api/checkout
/review-pr!
/review-code!
/research! How does connection pooling work in our app?
/implement! Add webhook retry with exponential backoff
```

Each skill accepts `$ARGUMENTS` — pass your topic, issue description, or feature request directly after the command.

## Design Philosophy

These skills exploit three well-documented principles:

**Cognitive diversity beats individual depth.** 3-4 distinct perspectives catch 80-90% of defects — diversity matters more than reviewer count (Fagan, IBM 1976). Independent agents with constrained viewpoints outperform a single generalist, the same mechanism behind random forests in ML and the Delphi method in expert forecasting (Surowiecki, "The Wisdom of Crowds").

**Parallel hypothesis testing beats serial investigation.** Enumerating all candidate causes before pursuing any prevents premature convergence (NASA Fault Tree Analysis). Structured post-mortems with parallel tracks reduce repeat incidents by 40-60% (Beyer et al., "Site Reliability Engineering", 2016). A single investigator cannot reliably falsify their own theory due to confirmation bias (Kahneman, "Thinking Fast and Slow").

**Communication overhead must be designed out, not managed.** Communication scales O(n²) with team size (Brooks, "The Mythical Man-Month"). The countermeasure: define interfaces before parallelizing so agents share contracts, not conversations. Interface-first decomposition yields 3-5x throughput gains in teams >4 (Forsgren et al., "Accelerate", 2018).

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

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- [oh-my-claudecode](https://github.com/anthropics/oh-my-claudecode) (for specialized agent types like `tracer`, `architect`, `security-reviewer`, etc.)

Without OMC, Claude Code will still attempt to spawn agents using the generic Agent tool — results may vary.

## License

[GPL-3.0](LICENSE)
