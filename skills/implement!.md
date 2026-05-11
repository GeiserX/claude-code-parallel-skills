Implement this feature using parallel execution. Decompose into independent units, define contracts, dispatch agents, integrate, verify.

$ARGUMENTS

---

## Phase 1: Plan & Decompose (sequential, you do this)

1. Analyze the feature. Identify 3-7 independent work streams (not more — coordination cost exceeds value past 7).
2. Determine the split axis:
   - **Vertical slices** (route + handler + DB + test per feature) — for independent features
   - **Horizontal layers** (types → backend → frontend → tests → docs) — for single features touching many layers
   - **File-level** (each agent owns specific files) — for repetitive patterns or codemods
3. Build the dependency graph. Identify what MUST be sequential (interfaces, schemas, migrations) vs what's truly parallel.
4. Define contracts: types, function signatures, API shapes, shared interfaces. These are the stable surface parallel agents code against.

## Phase 2: Scaffold (sequential, executor agent)

Spawn ONE architect agent (oh-my-claudecode:architect) to:
- Create directory structure and empty files with full type signatures
- Define all shared interfaces, types, schemas
- Write stub implementations / TODO markers for parallel agents to fill
- Output: the contract document (what each executor must implement and what they can depend on)

## Phase 3: Parallel Execution (launch ALL at once)

Spawn N executor agents (oh-my-claudecode:executor), one per work stream. Each gets:
- The contract document from Phase 2
- Their specific file ownership (non-overlapping — NO two agents touch the same file)
- Clear acceptance criteria (what "done" means for their slice)
- Instruction to write unit tests for their own code

Additional parallel agents as needed:
- **Test Engineer** (oh-my-claudecode:test-engineer) — integration/e2e tests spanning boundaries
- **Infrastructure** (oh-my-claudecode:executor) — CI, Docker, deployment configs (only if feature needs it)
- **Documentation** (oh-my-claudecode:writer) — API docs, README updates (only if public-facing)

## Phase 4: Integration (sequential)

1. Merge all outputs. Resolve any interface mismatches.
2. Wire components together (imports, dependency injection, routing).
3. Run full build + type check. Fix any compilation errors from cross-boundary issues.

## Phase 5: Verification (parallel)

Launch 3 verifiers simultaneously:
1. **Code Reviewer** (oh-my-claudecode:code-reviewer) — correctness, logic, quality
2. **Test Runner** (oh-my-claudecode:test-engineer) — run all tests, check coverage, add missing cases
3. **Architect** (oh-my-claudecode:architect) — does the implementation match the contracts? Any drift?

If any verifier rejects: fix and re-verify (max 3 rounds).

## Scaling

7 parallel agents (executors + specialists) is the MINIMUM for non-trivial features. For large features, spawn more:
- Additional executors for more work streams (one per module/slice)
- Multiple test engineers (unit vs integration vs e2e)
- Multiple documentation writers (API docs vs user guides vs inline)
- Domain-specific executors (one for auth, one for data layer, one for UI, etc.)

Scale to 10-15+ agents when the feature has many independent modules, repetitive patterns, or spans multiple services. More agents = faster delivery. Don't hold back.

## Rules

- NEVER spawn parallel agents before contracts are defined. Interface instability = rework.
- Each agent gets non-overlapping files. If two agents need the same file, it belongs to the earlier phase (scaffold).
- 7 parallel agents is the floor, not the ceiling. Scale up for larger features.
- If the task is small enough for one agent to do in one pass, skip this process entirely.
- Use worktree isolation when available for large features.
- Model routing: scaffold=opus, executors=sonnet (or opus for complex slices), verifiers=sonnet.
