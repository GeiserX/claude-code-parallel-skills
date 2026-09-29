# Parallel commands

The parallel commands work with Claude Code's Agent tool and can use
[oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode) (OMC) specialists when available.
The loops require OMC only for unattended continuation; without it, their saved state remains manually
resumable.

One-shot commands that fan out specialized agents in parallel, then synthesize.

## `/investigate!`

Root-cause analysis and fix. Spawns 7+ agents (tracer, scientist, architect, security reviewer, critic, test engineer, debugger) to investigate an issue simultaneously, synthesizes findings, then implements a fix with a reproduction test.

## `/review-pr!`

Multi-perspective PR review. Spawns 7+ simultaneous reviewers (logic, architecture, security, simplicity,
tests, performance, data integrity), then synthesizes into a severity-ranked verdict with a clear
APPROVE / REQUEST CHANGES / NEEDS DISCUSSION outcome.

## `/review-code!`

Whole-codebase health audit. Spawns 7+ auditors (architecture, security, complexity, dead code, error handling, test health, consistency) that examine the entire repo through orthogonal lenses, then synthesizes into a scored health report with prioritized actions.

## `/research!`

Deep parallel research on any topic. Spawns 7+ researchers (docs specialist, codebase explorer, git historian, comparativist, architect, critic, performance analyst) that attack the question from different angles, then synthesizes into actionable findings with options and trade-offs.

## `/implement!`

Parallel feature implementation. Decomposes work into independent streams, scaffolds contracts/interfaces first, then dispatches N executor agents working on non-overlapping files simultaneously. Finishes with parallel verification (code review + tests + architecture check).

## Review automation

## `/coderabbit-loop`

A bounded [CodeRabbit](https://coderabbit.ai) review-and-fix loop. It verifies each finding against the
current code, applies only valid minimal fixes, runs repository checks, pushes normally, and waits for a
review tied to the new head SHA. It stops at `READY` by default. Merge, release, and deployment are separate
explicit options with their own safety gates.
