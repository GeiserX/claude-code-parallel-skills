# Claude Code Parallel Skills

Four slash-command skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) that spawn multiple specialized agents in parallel to tackle complex tasks faster and more thoroughly.

These skills leverage [oh-my-claudecode](https://github.com/anthropics/oh-my-claudecode) agent types but work with any Claude Code setup that supports the Agent tool.

## Skills

### `/review-pr!`

Multi-perspective PR review. Spawns 6+ simultaneous reviewers (logic, architecture, security, simplicity, tests, performance), then synthesizes findings into a severity-ranked verdict with a clear APPROVE / REQUEST CHANGES / NEEDS DISCUSSION outcome.

### `/research!`

Deep parallel research on any topic. Spawns 7+ researchers (docs specialist, codebase explorer, git historian, comparativist, architect, critic, performance analyst) that attack the question from different angles, then synthesizes into actionable findings with options and trade-offs.

### `/investigate!`

Root-cause analysis and fix. Spawns 7+ agents (tracer, scientist, architect, security reviewer, critic, test engineer, debugger) to investigate an issue simultaneously, synthesizes findings, then implements a fix with a reproduction test.

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
/review-pr!
/research! How does connection pooling work in our app?
/investigate! Users getting 500 errors on /api/checkout
/implement! Add webhook retry with exponential backoff
```

Each skill accepts `$ARGUMENTS` — pass your topic, issue description, or feature request directly after the command.

## Design Principles

- **Parallel by default** — all agents launch simultaneously, no sequential bottlenecks
- **Minimum floors, not ceilings** — each skill defines a minimum agent count (6-7) but scales up for larger tasks (10-15+)
- **Contracts before code** — `implement!` scaffolds interfaces first so parallel agents don't conflict
- **Evidence over opinion** — agents report confidence levels and cite sources/files/lines
- **Synthesize, don't aggregate** — cross-reference findings, flag contradictions, deduplicate

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- [oh-my-claudecode](https://github.com/anthropics/oh-my-claudecode) (for specialized agent types like `tracer`, `architect`, `security-reviewer`, etc.)

Without OMC, Claude Code will still attempt to spawn agents using the generic Agent tool — results may vary.

## License

[GPL-3.0](LICENSE)
