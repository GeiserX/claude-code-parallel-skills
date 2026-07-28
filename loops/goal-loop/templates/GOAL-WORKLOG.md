# Goal Loop Worklog

- State format: `1`
- Segment: `1`
- State directory: `{{STATE_DIR}}`
- Continuation: `{{CONTINUATION_MODE}}`
- Limits: iterations `{{MAX_ITERATIONS}}`; no progress `{{NO_PROGRESS_LIMIT}}`; failures `{{FAILURE_LIMIT}}`
- Segment-start counters: iteration `0`; no progress `0`; failures `0`
- Started: `{{TIMESTAMP}}`

This file is append-only. Each claim must include evidence that was actually observed.

## Iteration 0 — preflight

- Repository: `{{REPOSITORY}}`
- Canonical repository root: `{{CANONICAL_REPOSITORY_ROOT}}`
- Git common directory: `{{GIT_COMMON_DIR}}`
- Branch: `{{BRANCH}}`
- Initial dirty paths: {{INITIAL_DIRTY_PATHS}}
- Repository instructions read: {{INSTRUCTIONS_READ}}
- Verification commands discovered: {{VERIFICATION_COMMANDS}}
- OMC persistence: {{OMC_STATUS}}
- Next action: {{NEXT_ACTION}}
- Stop decision: continue

<!-- Append iterations using this structure:

## Segment S · Iteration N — TIMESTAMP

- Planned slice:
- Files changed:
- Verification evidence:
- Fresh-review evidence:
- Progress evidence:
- Consecutive failures:
- Consecutive no-progress iterations:
- Reversible defaults:
- Remaining work:
- Next action:
- Stop decision: continue | SUCCESS | BLOCKED | BUDGET | ERROR | CANCELLED

For a terminal entry, also record the exact reason, OMC cancellation evidence, and manual resume invocation.

For each explicit continuation, append a segment-start entry with the new segment number, timestamp,
current limits, and counters reset to iteration `0`, no progress `0`, and failures `0`. Never remove or
renumber counters from earlier segments.
-->
