Comprehensive multi-perspective review of this PR. Spawn all reviewers simultaneously, synthesize into a severity-ranked verdict.

$ARGUMENTS

---

## Setup

Get the full diff (`git diff main...HEAD`), PR description, and affected modules. Give the FULL diff to every reviewer.

## Review Panel (launch ALL at once)

Each reviewer outputs findings as: SEVERITY (critical/high/medium/low/nit) + file:line + description.

1. **Logic Auditor** (oh-my-claudecode:code-reviewer) — Correctness. Off-by-ones, null handling, edge cases, invariant violations. "Does every execution path produce the correct result?"
2. **Architect** (oh-my-claudecode:architect) — Design coherence. Coupling, abstraction level, information hiding. "Will this make the system harder to change later?"
3. **Security Reviewer** (oh-my-claudecode:security-reviewer) — Injection, auth gaps, secrets, trust boundaries, SSRF. "How would I exploit this?"
4. **Simplicity Advocate** (oh-my-claudecode:critic) — YAGNI violations, premature abstractions, dead code, unnecessary indirection. "Can this be simpler?"
5. **Test Assessor** (oh-my-claudecode:test-engineer) — Test adequacy, coverage, flakiness, missing edge cases. "If someone breaks this tomorrow, will tests catch it?"
6. **Concurrency & Perf** (oh-my-claudecode:scientist) — Race conditions, resource leaks, N+1, unbounded allocations. Reports "N/A" if PR has no concurrency/perf surface.
7. **Data Integrity** (oh-my-claudecode:code-reviewer) — Persistence, migrations, transactional boundaries, schema compatibility, and recovery. Reports "N/A" when the PR has no stateful data surface.

## Output

```text
## PR Review: [title]

### Verdict: [APPROVE | REQUEST CHANGES | NEEDS DISCUSSION]

### Critical (blocks merge)
- [file:line] description — [role]

### High (should fix before merge)
- [file:line] description — [role]

### Medium (recommended)
- [file:line] description — [role]

### Low / Nits
- [file:line] description — [role]

### Positive
- [things done well]
```

## Scaling

The 7 reviewers above are the MINIMUM baseline. For large or cross-cutting PRs, spawn additional specialists:
- **API Designer** (oh-my-claudecode:critic) — interface ergonomics, backwards compatibility, naming
- **Domain Expert** (oh-my-claudecode:scientist) — business logic correctness for the affected domain
- **DevOps/Infra** (oh-my-claudecode:scientist) — deployment impact, config changes, CI implications
- **Accessibility/UX** (oh-my-claudecode:scientist) — if frontend/UI changes exist
- **Error Handling** (oh-my-claudecode:code-reviewer) — failure paths, recovery, observability

Scale to 10-12+ reviewers for large PRs touching multiple modules, or PRs with both frontend + backend + infra changes. More eyes = fewer missed issues. Don't hold back.

## Rules

- All reviewers launch simultaneously.
- 7+ is the floor for non-trivial PRs. Add more for large changesets.
- PR < 20 lines: only Logic Auditor + Security + Simplicity.
- PR is docs-only: report "No code review needed" and exit.
- Deduplicate same issue found by multiple reviewers (keep highest severity).
