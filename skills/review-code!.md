Comprehensive multi-perspective review of this entire codebase. Spawn all auditors simultaneously, synthesize into a prioritized health report.

$ARGUMENTS

---

## Setup

1. Get a high-level picture: directory structure, entry points, package.json/go.mod/Cargo.toml, README.
2. Identify key areas: src/, lib/, api/, tests/, config/, infra/.
3. Each auditor reads the files relevant to their concern (best-effort — large repos won't fit in one pass, that's fine).

## Audit Panel (launch ALL at once)

Each auditor reads the codebase through their specific lens. Each outputs: SEVERITY (critical/high/medium/low) + file:line + description + consequence. Max 7 findings per auditor — only report what matters.

1. **Architecture Auditor** (oh-my-claudecode:architect) — Module boundaries, coupling direction, circular dependencies, layering violations, god modules. "Is the structure coherent and maintainable?"
2. **Security Auditor** (oh-my-claudecode:security-reviewer) — Hardcoded secrets, injection surfaces, auth/authz gaps, vulnerable dependencies, overly permissive configs, OWASP. "How would I break into this system?"
3. **Complexity Analyst** (oh-my-claudecode:scientist) — Cyclomatic complexity hotspots, god classes/functions, deeply nested logic, methods >50 lines, cognitive load. "Where will the next bug come from?"
4. **Dead Code Hunter** (oh-my-claudecode:explore) — Unused exports, unreachable branches, orphan files, unused dependencies, stale feature flags. "What can be deleted?"
5. **Error Handling Reviewer** (oh-my-claudecode:code-reviewer) — Swallowed errors, inconsistent patterns, missing error boundaries, silent failures, inadequate logging. "When this breaks in prod, will anyone know?"
6. **Test Health Assessor** (oh-my-claudecode:test-engineer) — Coverage gaps, test-to-code ratio, flaky patterns, tests testing implementation vs behavior, missing integration tests. "If someone breaks this tomorrow, will tests catch it?"
7. **Consistency Checker** (oh-my-claudecode:critic) — Naming conventions, pattern uniformity across modules, style violations linters miss, conflicting approaches to the same problem. "Does this look like one team wrote it?"

## Scaling

The 7 auditors above are the MINIMUM baseline. For larger or more complex codebases, spawn additional specialists:
- **Documentation Auditor** (oh-my-claudecode:critic) — do docs match implementation? Missing docs? Outdated examples?
- **API Surface Reviewer** (oh-my-claudecode:architect) — public API coherence, naming, backwards compatibility concerns
- **Performance Reviewer** (oh-my-claudecode:scientist) — N+1 patterns, unbounded allocations, memory leaks, hot path analysis
- **DevOps/Config Reviewer** (oh-my-claudecode:scientist) — CI/CD health, Dockerfile quality, env var management, deployment config
- **Type Safety Auditor** (oh-my-claudecode:code-reviewer) — `any` abuse, unvalidated type assertions, schema drift between layers

Scale to 10-12+ auditors for monorepos, multi-service repos, or codebases with both frontend + backend + infra. Don't hold back.

## Output

```
## Codebase Health Report: [repo name]

### Overall Score: [X/10]

### Critical (immediate action needed)
- [file:line] description — consequence — [CATEGORY] — found by [role]

### High (should fix soon)
- [file:line] description — consequence — [CATEGORY] — found by [role]

### Medium (degrades over time)
- [file:line] description — consequence — [CATEGORY] — found by [role]

### Metrics
| Dimension | Score | Notes |
|-----------|-------|-------|
| Architecture | X/10 | ... |
| Security | X/10 | ... |
| Complexity | X/10 | ... |
| Dead Code | X/10 | ... |
| Error Handling | X/10 | ... |
| Test Health | X/10 | ... |
| Consistency | X/10 | ... |

### Top 5 Actions (prioritized)
1. [Most impactful fix]
2. ...

### Strengths
- [Things done well — acknowledge good patterns]
```

Categories: [ARCH], [SEC], [CMPLX], [DEAD], [ERR], [TEST], [CONS], [DOCS], [PERF], [DEPS]

## Synthesis

After all auditors report:
1. Deduplicate (same issue from different angles counts once, highest severity).
2. Cap total findings at 20. If more, keep only HIGH+ severity.
3. Compute per-dimension scores (10 = no issues found, 1 = critical problems).
4. Produce Top 5 Actions ranked by: severity * blast radius * fix effort.

## Rules

- All auditors launch simultaneously.
- 7 is the floor, not the ceiling. Add more for larger codebases.
- Best-effort reading — agents focus on their area's most relevant files, not every line.
- Every finding must state the CONSEQUENCE ("what breaks, when, for whom"). No vague "consider refactoring."
- This is a health report, NOT a pass/fail gate.
- Brief each auditor with: repo name, tech stack, directory structure, and their specific mandate.
