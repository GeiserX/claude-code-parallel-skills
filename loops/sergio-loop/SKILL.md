---
name: sergio-loop
description: A single-entry autonomous loop for a repo. Copies your directive verbatim into docs/GOAL.md (so the work is remembered across sessions/compaction), engages OMC persistent mode (ralph by default, autopilot for idea→code) so it runs hands-off, then drives research → implement → review-pr on repeat until the goal holds — logging to docs/AUTOPILOT-WORKLOG.md and parking genuine human-only calls in docs/DEFERRED-QUESTIONS.md. Runs cancel on completion to clean up state. Add "coordinate" to join a shared docs/COORDINATE.md board so two sessions on the same repo remember and check each other. Use when the user runs /sergio-loop, says "run the loop", "go autopilot on this", or "drive the loop".
argument-hint: "[coordinate] <your directive — copied verbatim into GOAL.md>"
---

# /sergio-loop — write the goal, then run the loop

The single entry point for driving one repo to a goal, hands-off. You give it a directive; it (1) saves
that directive **verbatim** to `docs/GOAL.md` so the work is remembered across sessions/compaction, (2)
**engages OMC's persistent mode (ralph by default, autopilot for idea→code)** so the loop runs hands-off
and the boulder never stops, then (3) drives the autonomous **research → implement → review** cycle until
the goal is met.

This skill does **not** call Claude Code's native `/goal` (the model can't invoke that; only you can type
it). If you ever want CC's native goal-loop *on top*, type `/goal <condition>` yourself — optional,
independent.

> **Depends on [oh-my-claudecode](https://github.com/anthropics/oh-my-claudecode):** the loop is glue over
> OMC's `ralph` / `autopilot` / `cancel` skills and the `/research!` `/implement!` `/review-pr!` commands
> from this repo. Install those first (see the repo README).

## Step 0 — parse args
- If the args contain **`coordinate`** (or `coord`, `team`, `two`) → **coordination mode ON**; strip that
  word out. Otherwise **solo** (the default — solo is totally fine).
- **Everything else in the args is the directive**, captured verbatim for `GOAL.md`.

## Step 1 — GOAL.md = the directive, verbatim (nothing more)
Target repo = current working directory (or a path the user named). Docs go in `<repo>/docs/` (create if
missing).
- **No `docs/GOAL.md` yet:** write it from `templates/GOAL.md` — just the verbatim directive + today's
  date (`date +%F`). Do **not** add derived mission/constraints/plan sections; GOAL.md is a faithful
  copy-paste of what the user sent, so re-reading it later reminds us what we were doing. Nothing more.
- **`docs/GOAL.md` already exists:** don't overwrite. Append a dated section
  `## <date> — continuation` followed by the new message verbatim. (Append-only history of directives.)
- If no directive was passed and no GOAL.md exists, ask the user for one line of intent — the only
  acceptable upfront question, since without a goal there's nothing to loop on.

## Step 2 — recover state (so the loop resumes, never restarts)
Read, if present: `docs/GOAL.md` (what we're doing), the **bottom** of `docs/AUTOPILOT-WORKLOG.md` (where
we left off), and `docs/DEFERRED-QUESTIONS.md` (open human-only items). These files survive context loss;
resume from them. Create `docs/AUTOPILOT-WORKLOG.md` from its template on first run.

## Step 2.5 — engage OMC persistent mode (so it's hands-off automatically)
Put OMC into a persistent autonomous mode so the loop keeps running without pings — this is what makes
`/sergio-loop` "autopilot with ralph", not a one-shot:
- **Default → `ralph`:** invoke the **`ralph`** skill with `<goal from GOAL.md>`. Ralph is the persistence
  engine ("the boulder never stops") — it keeps iterating until the goal's acceptance criteria pass and a
  reviewer verifies. Best for an existing repo with a known goal (the usual case).
- **Idea→code from scratch → `autopilot`:** for a bare product idea (no repo/scaffold), invoke the
  **`autopilot`** skill instead — expansion → planning → execution → QA → validation.
- **Sandboxed/headless environments:** if you run in a sandbox where OMC's own Stop hook isn't loaded,
  invoking `ralph` alone won't re-fire (the loop stops at every turn-end). There you need an equivalent
  persistence mechanism — a Stop hook plus a small state flag (`{active, expires_at, iter, max_iter,
  prompt}`) that the hook reads to decide whether to keep going. On a normal local setup with OMC
  installed, ralph's own state handles persistence and you don't need this.
- The research → implement → review cycle below (Step 5) is the **work performed inside** that persistent
  mode. When a Stop hook emits "The boulder never stops", keep iterating — do real work each turn.
- **Clean exit is mandatory:** when the goal is met (or you must stop), invoke the **`cancel`** skill (and
  clear any persistence flag you set). Do NOT leave persistence active — a stuck flag / dangling
  autopilot-state re-fires the Stop hook forever.

## Step 3 — set the loop (+ optional knowledge-base overlay)
**The core loop is the same everywhere:** `/research! → /implement! → /review-pr!`, then **back to
`/research!`** for the next slice — cycling until the goal holds. Each review feeds the next research.

**Optional: a knowledge-base overlay.** Some repos have a place that answers *"what do we actually intend
here?"* — a product/architecture knowledge base, a decisions doc, a wiki, or a project-specific research
command you maintain. **If yours does, point the loop at it** (fill in the placeholder below) and let it
consult that source to settle intent *before* parking a question as human-only. It is **not** a fixed step
of the cycle — run it about once per cycle as a baseline, but use judgement: skip cycles where intent is
already clear, run it more when a step needs heavy alignment. It's also your FIRST move for anything that
smells like a deferred question: try the knowledge base before parking it for a human.

```
KNOWLEDGE_BASE = <fill me in, or leave empty>
    e.g.  docs/KNOWLEDGE.md   |   an internal wiki URL   |   a /your-research! command   |   (none)
```

If `KNOWLEDGE_BASE` is empty, just run the core loop — no overlay.

**Honor the repo's conventions.** If the repo (or a parent dir) has a `CLAUDE.md` / `AGENTS.md` /
`CONTRIBUTING.md`, read it once and follow it: commit style (conventional commits by default), the PR
workflow, CI expectations (keep it green — `gh run watch`), and any house rules. Never change global git
config.

## Step 4 — coordination mode (only if ON)
The shared `docs/COORDINATE.md` IS the cross-session memory — how two sessions "remember both sides".
- Scaffold it from the template if missing; pick a one-word **lane name** for this session (ask if
  unclear). Register the lane and post a dated kickoff entry under `## Log`.
- **Top of every iteration:** re-read the whole board; **ACK** anything new from the other lane (status →
  `ACK`, one-line reply) before doing your own work; update "What I'm doing NOW".
- **Bottom of every iteration:** append a dated, signed `### [date] <lane> — <subject>` entry (what
  shipped, any `ASK`, any `CLAIMING`/`RELEASING` of a shared resource). Newest at the bottom.
- **Hard rules:** never edit the other lane's repo files (raise an `ASK`); single-driver shared resources
  need `CLAIMING` + an `ACK` before use; announce contract changes before merge. Coordination overhead is
  expected and fine — it's the price of two AIs on one task without clobbering each other.

## Step 5 — RUN THE LOOP (the boulder never stops)
The cycle is **`/research!` → `/implement!` → `/review-pr!` → back to `/research!`**, repeating until the
goal in `GOAL.md` is met or a *real* roadblock appears. Each cycle:
1. **`/research!`** the next build question.
2. **`/implement!`** the next concrete step (track work in your issue tracker if you use one — GitHub
   issues, beads, etc.).
3. **`/review-pr!`** / verify — keep CI green; run gates locally first where practical.
4. **Record:** append a worklog entry — what shipped + tally `X closed / Y open` + test/CI-run/commit
   evidence. In coordination mode, also post + ACK on the board.
5. **Commit** (conventional commits; PR workflow per the repo's conventions; never merge without approval /
   green CI).
6. **Loop back to step 1** (`/research!`) for the next slice — the review just done informs what to
   research next.

**Knowledge-base overlay (only if `KNOWLEDGE_BASE` is set):** consult it *around* this cycle to stay
aligned with intent — roughly once per cycle as a baseline, more when a step needs heavy alignment, less
when intent is already clear. Weave it in before `/research!` or `/implement!` whenever alignment is the
bottleneck, and always as the first attempt to resolve a would-be deferred question.

**Autonomy contract:** go uninterrupted; defer instead of asking. For each would-be question, first try the
knowledge base (or `/research!`); only if it genuinely needs *the user's* judgement (irreversible /
product / scope / legal / external blocker) do you pick the reversible default, record it in
`DEFERRED-QUESTIONS.md` (Context / Default-taken / To-change), and keep moving. Surface to the user ONLY a
roadblock that blocks all forward progress.

## Templates & docs
Templates live in this skill's `templates/` dir. Fill every `{{PLACEHOLDER}}` — leave none
literal (`{{DATE}}`=`date +%F`, `{{REPO}}`=repo name, `{{DIRECTIVE}}`=verbatim message, `{{LANE}}`=lane
name, `{{LOOP_LINE}}`=the core loop `/research! → /implement! → /review-pr! → (back to research)`,
`{{COORD_NOTE}}`=`, COORDINATE.md` if coordinating else empty).
- **Always:** `GOAL.md` (verbatim), `AUTOPILOT-WORKLOG.md` (append-only, newest at bottom, evidence on
  every "done").
- **Coordination:** `COORDINATE.md`.
- **As needed:** `DEFERRED-QUESTIONS.md` (only when a real human-only item arises — ideally stays
  near-empty because the knowledge-base overlay absorbs most), and the optional `PROGRESS.md` /
  `CONTRACT.md`.

## When to stop
- Goal met and verified (CI green, evidence recorded) → **invoke `cancel`** to tear down persistent-mode
  state, then report and stop.
- A real roadblock or genuine human-only decision blocks ALL progress → record it, **invoke `cancel`**,
  surface it, stop.
- Otherwise keep looping — don't stop just because one iteration finished (the boulder never stops).
- Always `cancel` before fully stopping — never leave ralph/autopilot state behind (a dangling state file
  makes the Stop hook block every future turn).

## Rules
- GOAL.md is a verbatim copy of the directive — never editorialize it.
- Engage `ralph` (default) or `autopilot` (idea→code) for hands-off persistence; always invoke `cancel`
  before fully stopping.
- Solo is the default; coordination is opt-in via `coordinate`.
- Honor the repo's `CLAUDE.md`/conventions. No AI attribution in commits/PRs/docs. Don't fabricate
  progress — a worklog "done" needs real evidence (tests, CI run, commit).
