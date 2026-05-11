Investigate and fix this issue. Spawn all agents simultaneously, synthesize findings, implement the fix.

$ARGUMENTS

---

## Phase 1: Parallel Investigation (launch ALL at once)

Each agent gets: the issue description, affected file paths, and their specific lens. Each outputs findings with CONFIDENCE (high/medium/low) and SEVERITY.

1. **Tracer** (oh-my-claudecode:tracer) — Follow execution from trigger to failure. Output: timeline of state changes, exact divergence point.
2. **Scientist** (oh-my-claudecode:scientist) — Form 3-5 competing hypotheses, run experiments to falsify. Output: ranked hypotheses with evidence.
3. **Architect** (oh-my-claudecode:architect) — Why did the design permit this? Blast radius? Deeper structural issue? Output: design-level root cause.
4. **Security Reviewer** (oh-my-claudecode:security-reviewer) — Exploitable? Trust boundary violation? Output: threat assessment, OWASP class.
5. **Critic** (oh-my-claudecode:critic) — Challenge every assumption. What is everyone taking for granted? Output: unverified assumptions, alternative explanations.
6. **Test Engineer** (oh-my-claudecode:test-engineer) — What tests should exist but don't? What regressions could a fix introduce? Output: proposed test cases.
7. **Debugger** (oh-my-claudecode:debugger) — Reproduce it. Read logs, check stack traces, inspect state. Output: minimal repro steps, exact error.

## Phase 2: Synthesize

Cross-reference findings. Flag conflicts. Rank by confidence + severity. Identify the true root cause.

## Phase 3: Fix

1. Write reproduction test (must fail before fix).
2. Implement fix targeting root cause.
3. Run full test suite.
4. Have Critic confirm the fix addresses root cause, not just the symptom.

## Scaling

The 7 agents above are the MINIMUM baseline. For complex or multi-layered issues, spawn additional specialists:
- **DevOps/SRE** (oh-my-claudecode:scientist) — environment differences, deployment issues, infra-level causes
- **Data Analyst** (oh-my-claudecode:scientist) — log mining, metric correlation, anomaly detection
- **Concurrency Specialist** (oh-my-claudecode:scientist) — race conditions, deadlocks, ordering issues
- **Performance Engineer** (oh-my-claudecode:scientist) — profiling, resource leaks, hot paths
- **Domain Expert** (oh-my-claudecode:scientist) — business logic correctness, domain-specific edge cases

Scale to 10-12+ agents when the issue spans multiple systems, involves concurrency, or has unclear reproduction. More perspectives = faster convergence on root cause. Don't hold back.

## Rules

- All agents launch simultaneously.
- 7 is the floor, not the ceiling. Add more agents for complex issues.
- If trivial (typo, missing import), skip the panel and fix directly.
- Brief each agent with full context — they have zero prior knowledge.
