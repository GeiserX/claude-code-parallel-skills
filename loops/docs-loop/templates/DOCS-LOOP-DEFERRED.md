# DOCS-LOOP DEFERRED — doc calls that need a human

> Human-only doc decisions the loop parked instead of guessing. Each stays until the maintainer resolves it;
> resolutions are stamped and appended, **never deleted** — the trail is the point. Ideally this file stays
> nearly empty, because your knowledge base (if you've configured one) should settle most intent first.

Defer here (do NOT auto-edit) when a doc change needs judgment the code can't give: a roadmap/strategy/
positioning call, an ambiguous stale section where you'd be guessing, a product decision (deprecate/rename a
public thing), licensing/legal/security-posture wording, deleting a doc, or anything that would name a person
or cite a call.

---

<!-- Append one block per deferred call. Copy this shape:

## #{{N}} — {{REPO}}: {{one-line title}}   ({{DATE}})
- **Context:** what the doc says now, what the code shows, why it can't be settled from code alone.
- **Default taken:** the reversible thing done meanwhile (usually: left the doc as-is / annotated, opened no
  PR for this doc). The confident edits in the same repo still shipped.
- **To change:** the exact edit to make once the maintainer decides — and the question for them, in one line.
- **Knowledge base consulted?** yes/no — and what it returned.
- _Resolved {{DATE}}:_ <appended when the maintainer answers; the original block is left intact above>
-->
