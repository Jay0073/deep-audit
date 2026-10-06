# Deep Audit

A stack-agnostic, decision-oriented audit skill for AI coding agents. Point it
at a project or one part of it - a user journey, page, API, worker, pipeline,
SQL script, or library - and it verifies defects against real behavior before
deciding which ones deserve work now.

[[Available on Skills.sh]<img width="1467" height="873" alt="image" src="https://github.com/user-attachments/assets/9d9e3923-eae5-42c5-ae24-af6d553553e3" />
](https://www.skills.sh/jay0073/deep-audit/deep-audit)

It can cover the **whole product**, not just the code: correctness and data
bugs, user journeys, accessibility, reliability, and security. Its default is
not "find as many issues as possible." It prioritizes for the current release,
users, and expected scale, then separates immediate work from risks that should
wait for a measurable future trigger.

## What makes it different

- **Decision-oriented** - distinguishes fix-now work from fix-next and do-not-fix-yet risks.
- **Current-stage judgment** - a 10,000-user bottleneck is not a launch blocker without evidence that scale is near.
- **Intent-controlled detail** - supports blockers-only, important, or every reproducible issue, including minor UI polish when explicitly requested.
- **Journey-first UX** - tests connected user goals end to end in a real browser and verifies the underlying saved or computed result.
- **Adaptive depth** - maps a whole project, then spends agents and context on its highest-value current risks.
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

There is no build step or runtime dependency. Copy `SKILL.md` and the
`references` directory into your tool's skills directory:

```
SKILL.md     -> ~/.claude/skills/deep-audit/SKILL.md
references/ -> ~/.claude/skills/deep-audit/references/
```

## Usage

```
/deep-audit <target>                              # standard audit for current users
/deep-audit <target> "<hunch>"                    # start by proving or disproving a concern
/deep-audit pricing logic                         # execute focused logic tests and counterexamples
/deep-audit this app before launch                # release blockers at near-term scale
/deep-audit checkout UX with browser testing      # journey-first UX audit
/deep-audit the entire UI, include every minor issue # exhaustive UI and polish findings
/deep-audit worker exhaustive for 10k jobs/hour   # harden for an explicit future target
```

`<target>` may be a whole project or one unit: a journey, page, API surface,
background worker, data pipeline, migration set, CLI command, or library.

## How it works

`deep-audit` first establishes the lens, coverage, finding threshold, decision
horizon, and important users. It maps features into connected user journeys and
pairs browser-level behavior with the underlying requests, state changes, and
data writes. It verifies candidates against real behavior and reports under
`Fix before release`, `Fix next`, optional `Minor and polish issues`, and
`Watch - do not fix yet` with a trigger for reconsideration.

Specialized guidance is loaded only when needed. A UX audit does not spend
context on worker and SQL checklists, and a focused module audit does not force
a fixed number of agents or findings.

## License

[MIT](LICENSE)
