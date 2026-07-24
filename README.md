# Deep Audit

A stack-agnostic audit skill for AI coding agents (Claude Code, and any tool that
supports the `SKILL.md` format). Point it at one part of a project - a page, an API,
a background worker, a data pipeline, a SQL script, a library - and it reads the
code end to end, verifies its findings against real runtime data, and returns
plain-language issue rows you can paste straight into a tracker.

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

### Claude Code (one command)

```
/plugin marketplace add Jay0073/deep-audit
/plugin install deep-audit@deep-audit
```

Then run `/deep-audit`.

### Any other agent tool (manual)

The skill is a single self-contained `SKILL.md` - no build step, no dependencies.
Copy the skill folder into your tool's skills directory:

```
plugins/deep-audit/skills/deep-audit/   ->   <your tool's skills directory>/deep-audit/
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

There is no build step. A skill is a single Markdown file with a short header and a
set of instructions the agent loads when your request matches. `deep-audit` classifies
the target, maps everything that reads *and writes* its data, investigates in parallel,
verifies each finding against real behaviour, and reports only what survives.

## License

[MIT](LICENSE)
