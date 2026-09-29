# Installation and usage

## Installation

**Parallel commands** → your commands directory:

```bash
mkdir -p ~/.claude/commands
cp skills/*.md ~/.claude/commands/          # user-level (all projects)
# or, per-project:
mkdir -p .claude/commands && cp skills/*.md .claude/commands/
```

**Autonomous loops** → your skills directory (copy the whole directory, templates and references included):

```bash
mkdir -p ~/.claude/skills
cp -R loops/goal-loop loops/refine-loop loops/docs-loop ~/.claude/skills/
```

## Usage

```text
/investigate! Users getting 500 errors on /api/checkout
/review-pr!
/review-code!
/research! How does connection pooling work in our app?
/implement! Add webhook retry with exponential backoff
/coderabbit-loop

/goal-loop Ship OAuth device-flow login and get CI green
/refine-loop --target=. theta=1.5 focus=usability make the CLI genuinely pleasant to use
/docs-loop --root=~/src
/docs-loop --apply --open-prs ~/src/project-a ~/src/project-b
```

Each command accepts `$ARGUMENTS`. Mutating and outward-facing loop options are intentionally explicit;
read each loop's authorization section before invocation.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- Git
- [GitHub CLI](https://cli.github.com/) with appropriate authentication for `/coderabbit-loop` and
  `/docs-loop --open-prs`
- [oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode) — optional specialist agents for the
  parallel commands and required only for unattended loop continuation

Repository-provided tests and linters remain authoritative. `/docs-loop --open-prs` requires `gitleaks`;
optional tools such as `lychee` and Mermaid parsers are used when already installed. The loops do not
silently install tools.
