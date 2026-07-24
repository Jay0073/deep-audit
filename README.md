# Deep Audit

A stack-agnostic audit skill for AI coding agents. Point it at one part of a
project - a page, an API, a background worker, a data pipeline, a SQL script, a
library - and it reads the code end to end, verifies its findings against real
runtime data, and returns plain-language issue rows you can paste straight into
a tracker.

It audits the **whole project**, not just the code: correctness and data bugs,
missing functionality, UI/UX and accessibility gaps, reliability and failure
handling, and security. It reports only what it can prove, and it lists what it
ruled out.

## What makes it different

- **Whole-project scope** - code, database, UI/UX, workers, pipelines, security - not one layer.
- **Verified, not guessed** - findings are checked against real data before they are reported.
- **Report-only** - it finds and explains issues; it never edits your code during an audit.
- **Plain language** - one issue per row, no jargon, ready for a tracker.

## Install

Works with any agent that reads the `SKILL.md` format - Claude Code, Cursor,
Codex, and others.

### One command (recommended)

Requires Node. This fetches the skill straight from GitHub and installs it into
your agent's skills directory (`.claude/skills/`, `.agents/skills/`, and so on):

```
npx skills add Jay0073/deep-audit
```

GitHub is the registry, so there is no publishing step - this works the moment
the repo is public. No separate marketplace listing required.

Then run `/deep-audit`.

### Manual

The skill is a single self-contained `SKILL.md` - no build step, no dependencies.
Copy it into your tool's skills directory:

```
SKILL.md   ->   ~/.claude/skills/deep-audit/SKILL.md
```

## Usage

```
/deep-audit <target>              # audit one module, page, or service
/deep-audit <target> "<hunch>"    # seed it with what feels wrong
/deep-audit                       # it asks what to audit
```

`<target>` is any unit of the project: a page, an API surface, a background worker,
a data pipeline, a SQL script, a set of migrations, a CLI command, or a library module.

## How it works

There is no build step. A skill is a single Markdown file with a short header and
a set of instructions the agent loads when your request matches. `deep-audit`
classifies the target, maps everything that reads *and writes* its data,
investigates in parallel, verifies each finding against real behaviour, and
reports only what survives.

## License

[MIT](LICENSE)
