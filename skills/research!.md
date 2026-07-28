Deep parallel research on this topic. Spawn all researchers simultaneously, synthesize into actionable findings.

$ARGUMENTS

---

## Research Panel (launch ALL at once)

Each researcher gets: the topic/question, relevant context, and their specific angle. Each outputs findings with CONFIDENCE and sources/evidence.

1. **Documentation Specialist** (oh-my-claudecode:document-specialist) — Official docs, APIs, specs, changelogs. Find the authoritative source of truth. Output: relevant doc excerpts, version-specific details, official recommendations.
2. **Codebase Explorer** (oh-my-claudecode:explore) — How does this work in our code right now? Grep for usage patterns, find existing implementations, trace data flow. Output: relevant files/lines, current patterns, how we already handle this.
3. **Historian** (oh-my-claudecode:scientist) — Git history, prior decisions, related PRs/issues. Why was it built this way? What was tried before? Output: timeline of relevant changes, original rationale, past attempts.
4. **Comparativist** (oh-my-claudecode:scientist) — Alternative approaches. How do other projects/libraries/companies solve this? Pros/cons of each. Output: 3-5 alternatives with trade-offs.
5. **Architect** (oh-my-claudecode:architect) — Systemic implications. How does this interact with the rest of the system? What constraints exist? What would break? Output: dependency map, constraints, integration points.
6. **Critic** (oh-my-claudecode:critic) — Pitfalls, gotchas, known issues. What could go wrong? What looks simple but isn't? What are others complaining about? Output: risks, edge cases, failure modes, common mistakes.
7. **Performance/Scale Analyst** (oh-my-claudecode:scientist) — Benchmarks, resource usage, scaling behavior. What happens at 10x/100x? Output: performance characteristics, bottlenecks, limits.

## Synthesis

1. Cross-reference findings. Flag contradictions between sources.
2. Separate facts (documented, verified) from opinions (one agent's interpretation).
3. Produce a recommendation with trade-offs, not a single "right answer."

## Output

```text
## Research: [topic]

### Key Findings
- [finding 1 — source/evidence]
- [finding 2 — source/evidence]

### Our Current State
- [how we handle this today, relevant code]

### Options
| Option | Pros | Cons | Effort |
|--------|------|------|--------|

### Recommendation
[what to do and why, with caveats]

### Open Questions
- [things that need human judgment or more investigation]
```

## Scaling

The 7 researchers above are the MINIMUM baseline. For broad or multi-faceted topics, spawn additional specialists:
- **Domain Expert** (oh-my-claudecode:scientist) — deep-dive into a specific sub-area of the topic
- **Second Comparativist** (oh-my-claudecode:scientist) — different angle or ecosystem comparison
- **Security/Compliance** (oh-my-claudecode:security-reviewer) — if the topic has trust/privacy/legal surface
- **UX/Frontend** (oh-my-claudecode:scientist) — if user-facing implications exist
- **DevOps/Infra** (oh-my-claudecode:scientist) — if deployment, scaling, or infrastructure is involved

Scale to 10-12+ researchers when the topic spans multiple domains, technologies, or has both internal and external dimensions. More agents = more coverage. Don't hold back.

## Rules

- All researchers launch simultaneously.
- 7 is the floor, not the ceiling. Add more researchers for broader topics.
- If the topic is narrow and code-only (e.g., "how does X function work"), 7 is fine.
- Brief each researcher with full context — they have zero prior knowledge.
- Prefer primary sources over speculation.
