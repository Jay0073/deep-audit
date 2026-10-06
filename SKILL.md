---
name: deep-audit
description: "Decision-oriented, intent-controlled audit of a project, feature, user journey, page, API, worker, pipeline, data layer, script, or library. Proves defects with real behavior, tests features from the user's perspective through their underlying logic, and distinguishes fix-now work, minor polish, and future risks. Can also turn an existing audit into sequential, read-only solution briefs and an implementation handoff. Use for logic/correctness, bug, launch-readiness, UX/UI, reliability, security, whole-project audits, or planning fixes from audit findings. Language and stack agnostic."
---

# /deep-audit

Find real defects, then make the harder judgment: which ones are worth the user's time now.

An audit is successful when it supports a decision, not when it produces a long list.

## Usage

```text
/deep-audit <target>
/deep-audit <target> "<hunch>"
/deep-audit <target> before launch
/deep-audit <journey> UX audit with browser testing
/deep-audit the entire UI, including every minor reproducible issue
/deep-audit <target> exhaustive hardening for <stated scale or threat model>
/deep-audit resolve <audit report> one issue at a time without editing the project
```

The target may be one unit or a whole project. For a whole project, map broadly and spend depth according to the requested coverage: prioritize current journeys by default, or attempt every inventoried feature when explicitly asked. Do not pretend every file received equal scrutiny.

## Non-negotiables

**Audit and resolution planning are read-only.** Do not edit or refactor the audited project in either phase. Implementation is a separate, explicitly authorized request.

**Prove the behavior.** A possible weakness, absent best practice, suspicious line, or theoretical failure is not a finding. Demonstrate the current path and observable consequence or omit it.

**Separate truth from priority.** A defect can be real and still not deserve work now. Evaluate current reach, likelihood, consequence, recovery, and product horizon before assigning an action.

**No issue quota.** Never search until a familiar number of issues appears. Zero, three, or thirty may all be correct. Stop when the promised coverage is complete and candidates at the selected finding threshold are exhausted.

**Honor the requested finding threshold.** In blocker or important-only audits, omit low-value polish. When the user explicitly requests deep UI detail, every minor issue, or all reproducible findings, include verified minor defects within the selected lens and label them as minor rather than filtering them out. Personal taste is not an objective defect unless it conflicts with a design system, product intent, internal consistency, or an explicit user request for design judgment.

**Stay read-only.** Use non-mutating observation where possible. If a runtime check needs a write, use disposable data or a transaction that is rolled back. Never touch production data or external systems without explicit authorization.

## 1. Establish the decision context

Infer these from the request, repository, current data, and product documentation before asking:

- The decision: release now, improve a live product, investigate a hunch, improve a journey, prepare for stated growth, or perform exhaustive hardening.
- The horizon: this release/current operation, the next iteration, or a stated future scale or threat model.
- The people and workflows that matter most now.
- Current or near-term load, data volume, deployment shape, and exposure.
- Any hunch the user supplied.
- The three independent audit settings:
  - **Lens:** UX/UI, logic, data, reliability, security, performance, or a combination.
  - **Coverage:** one flow, important journeys, every reachable feature, or the whole system.
  - **Finding threshold:** release blockers, important issues, or all reproducible issues including minor polish.

Ask one concise question only when the missing answer would materially change what belongs in `Fix before release`. Otherwise proceed with this default:

> Audit the requested target and its important user journeys for safe use by current and next-release users at observed or documented near-term scale. Report important issues; treat unobserved future scale and unbuilt features as deferred conditions, not present blockers.

State the inferred context at the top of the report so the user can correct it.

Explicit inclusions and exclusions override every default. If the user says to report only current, first-customer, launch-blocking, or major issues, omit future-risk and minor sections rather than adding them for completeness.

Publicly reachable security flaws, authorization failures, payment errors, privacy exposure, and plausible irreversible data loss are current concerns even for a small product. Do not dismiss them merely because traffic is low.

## 2. Choose the audit intent and profile

Do not collapse lens, coverage, and finding threshold into one vague idea of "depth."

- **Release blockers / critical only:** Report only defects that plausibly block the stated decision or create serious current harm.
- **Important issues:** Default. Report `Fix now`, `Fix next`, and material future risks only when they fall within the requested horizon; omit minor polish.
- **All reproducible issues:** Select when the user says deep UI, every minor issue, exhaustive detail, or equivalent. Report evidence-backed minor defects within the selected lens and coverage, including visual, interaction, consistency, and polish issues when UI is in scope. Do not promote them to blockers.

An explicit scope overrides the default filter. "Every minor UI issue" means exhaustive UI reporting, not exhaustive backend scalability analysis. "Major UX issues" means journey-level material problems, not every spacing difference.

- **Launch readiness:** Use when the user is publishing or shipping. Concentrate on core journeys, first-use experience, auth, payments if present, data integrity, deploy/runtime failures, and recovery. Put future scale in `Watch` only when future risks are within scope; otherwise omit it unless the launch target makes the risk imminent.
- **Standard:** Default for ordinary audits. Inspect the requested target deeply and follow defects across its live read/write boundaries.
- **Logic/correctness:** Trace representative inputs through decisions, state changes, writes, and outputs. Execute focused tests and counterexamples, compare duplicate implementations, and recompute important results independently.
- **UX journey:** Use when the request mentions UX, UI, usability, a page, browser testing, user journeys, user flows, end-to-end use, or testing features as a user. Read [references/ux-journey.md](references/ux-journey.md) and test the selected journeys in a real browser when the app is runnable.
- **Exhaustive hardening:** Use only when explicitly requested. Widen coverage to every reachable feature or risk boundary in scope and apply the selected finding threshold. Exhaustive still means reproducible, not speculative.
- **Hunch/incident:** Start with the reported symptom and the state transitions around it. Confirm or disprove that before broadening.

Profiles can combine, such as a launch-readiness UX audit. Match effort to the request. Do not silently turn a focused audit into an exhaustive one.

## 3. Map before reading deeply

Build a compact map of:

- entry points and primary user or operator journeys;
- each selected feature's persona, realistic starting point, goal, action sequence, visible success condition, and recovery path;
- whether its ordinary use is one-shot or iterative and, when iterative, the repeated input-result-adjustment loop;
- calls from the target to data stores and outside systems;
- everything that writes data the target later reads;
- trust boundaries, auth/tenant boundaries, money movement, and irreversible writes;
- configuration, feature flags, deployment assumptions, and realistic failure paths;
- tests and sibling implementations of the same rule.

For a whole project, first inventory routes, features, roles, services, jobs, data stores, and documented workflows. Connect related features into realistic journeys rather than testing pages in isolation. Rank areas by present user reach, business importance, irreversible harm, and recent or complex change. Then deep-read the selected slices. Broadly reading every file before forming a risk model wastes context and lowers judgment quality.

When coverage is "every feature," maintain a coverage matrix with `tested end to end`, `partially tested`, `blocked`, and `not tested`. Never turn sampled pages into a claim of exhaustive product coverage.

Load only the checklist that matches a selected slice:

- HTTP/RPC/GraphQL/webhook boundaries: [references/request-handler.md](references/request-handler.md)
- Queues, cron, schedulers, and event consumers: [references/async-worker.md](references/async-worker.md)
- ETL, imports, exports, sync, and reporting: [references/data-pipeline.md](references/data-pipeline.md)
- SQL, schema, migrations, and query modules: [references/data-layer.md](references/data-layer.md)
- CLI, maintenance scripts, shared libraries, and SDKs: [references/executable-library.md](references/executable-library.md)

Do not load unrelated checklists.

## 4. Investigate economically

Use the fewest independent workstreams that give meaningful parallel coverage. Delegate when at least two genuinely independent journeys or risk boundaries can be investigated without duplicating repository discovery. A small focused target may need no delegation; an explicit exhaustive audit may justify several workstreams. Never create a workstream to reach a preferred agent count.

Split work by a distinct journey or risk boundary, not by sending several general reviewers over the same files. For important interactive features, pair two perspectives when practical:

- a **black-box journey pass** uses the product as the persona would, without relying on source knowledge to decide what should happen;
- a **white-box logic pass** traces the same journey through requests, state changes, data writes, jobs, and responses.

The coordinator compares the visible result with the stored or computed result. This paired pass is especially valuable when individual functions work but the transition between features fails. It is optional for a purely visual or polish audit unless saved state or logic affects the visual claim.

Give each agent:

- the lens, coverage, finding threshold, decision context, horizon, and current scale;
- a bounded flow or file set;
- numbered claims to confirm or deny;
- required verdicts: `CONFIRMED`, `PARTIALLY CONFIRMED`, or `NOT AN ISSUE`;
- a requirement for `file:line` and runtime evidence where available;
- the affected user or operator, triggering conditions, consequence, and proposed action bucket;
- for an iterative tool or workspace, a requirement to change an input after the first result and judge input-result continuity at a representative viewport;
- a stop condition;
- this instruction: "Apply the selected finding threshold. In important-only mode, do not collect minor observations. In all-reproducible mode, include verified minor issues within the selected lens."

Map centrally before dispatch so agents do not each rediscover the repository. Do not have every agent read all documentation or rerun the same broad test. Merge duplicates by root cause. Treat delegated verdicts as evidence to reconcile, not report-ready conclusions: when browser behavior, screenshots, requests, saved state, logs, tests, or source disagree, inspect the conflict and make the narrowest claim the combined evidence supports. Independently re-derive severe, ambiguous, or stateful claims and every severe number. For a low-risk minor finding, one repeat in the same browser pass with suitable visual evidence is sufficient.

Investigate the cheapest decisive evidence first. Before starting a broad or slow run, identify the material uncertainty it should resolve. If focused evidence already settles the decision and promised coverage, stop; use exhaustive or scale-heavy testing only when the request or a remaining uncertainty calls for it. Stop on a future optimization, pure preference, or unreachable edge case when it falls outside the selected threshold. In an all-reproducible UI audit, continue through the promised visual and interaction states, but distinguish objective inconsistency from subjective design judgment.

Use these checks across all target types when relevant:

- zero, one, empty, missing, duplicate, maximum, and realistic malformed input;
- dependency failure, timeout, partial success, retry, and recovery;
- state changes across reload, navigation, tenant/account switch, restart, and duplicate execution;
- duplicate calculations with different filters, windows, sources, units, rounding, caps, or cache behavior;
- labels and promises whose semantics differ from the value actually computed;
- current data that contradicts assumed cardinality, ordering, uniqueness, or scale.

Avoid mechanically testing every theoretical boundary. Prefer boundaries that current inputs, public inputs, imports, integrations, or ordinary user mistakes can actually reach.

## 5. Observe real behavior

Adapt observation to the target: run focused tests, call the endpoint, use the browser, inspect dev or staging data, recompute an aggregate, compare input and output records, examine a query plan, or run a worker with a representative payload.

Static proof is acceptable for a deterministic path whose consequence is unambiguous. Runtime proof is required for UX claims, timing or scale claims, environment-dependent behavior, and claims about what a user actually sees.

For an interactive journey, verify both sides when available: the user-visible state and the underlying request, saved record, emitted event, or computed output. A passing click sequence is not enough when the system silently saved the wrong thing; correct internal logic is not enough when the user cannot discover or complete the sequence.

For a calculator, dashboard, editor, search/filter interface, comparison tool, or configuration workspace, the first valid result is not the end of the journey. Change a meaningful input, inspect the updated output, and compare it with the prior state at a representative working viewport. Check whether the controls, context, and result needed for that loop remain visible together or are reachable with low friction. Scrolling is not itself a defect; report it when repeated travel, lost context, memory burden, or layout movement materially degrades the core loop. A full-page screenshot does not establish good workspace ergonomics.

If the required environment is unavailable, distinguish `verified from code` from `observed at runtime` and lower confidence appropriately. Put runtime evidence in context too: a deliberately disabled, mocked, or missing dependency may prove a genuine recovery weakness without proving that the ordinary product path is broken. Do not present a code-only interface review as a completed UX audit or give a harness-induced failure the priority of a naturally reachable one.

Actively try to disprove every candidate that could be reported under the selected threshold. Record rejected hypotheses internally, but report only the few that answer an explicit hunch or provide important assurance.

## 6. Apply the action gate

Do not use technical severity alone. For every confirmed defect, answer:

1. Who encounters it under the current decision context?
2. Is the trigger observed, common, easy to reach, rare, or dependent on a future condition?
3. Does it block completion, produce a wrong decision, lose or expose data, mishandle money, damage trust, exclude a user, create recoverable friction, or merely affect polish?
4. Can the user recover, and will they understand how?
5. What valuable work would fixing it displace?

The finding threshold decides whether a low-impact defect appears at all. The action bucket says what to do with a reported defect. Place each reported item in exactly one bucket:

| Bucket | Gate |
|---|---|
| **Fix before release / Fix now** | A current or near-term user can plausibly reach it and the consequence is release-blocking, materially wrong, unsafe, irreversible, or seriously trust-damaging. |
| **Fix next** | It affects current users or operators and creates meaningful repeated friction or failure, but the main goal remains safely achievable. |
| **Minor / Polish** | Only when the selected threshold includes all reproducible issues. A small logic, visual, interaction, or consistency defect is proven but has limited current consequence. It is optional work, not a release blocker. |
| **Watch - do not fix yet** | The defect is real, but value appears only after a future load, deployment shape, feature, rare state, or adoption level. Name the measurable trigger that should reopen it. |
| **Omit** | It is speculative, unreachable, duplicate, unproven, a generic best practice with no demonstrated defect, or outside the requested lens. Pure design preference is omitted unless design judgment was explicitly requested. |

"Critical" requires both material impact and a plausible current path. A failure at 10,000 users is not critical for a launch with no evidence that this load is near. The same failure becomes current when measured headroom, growth plans, queue depth, latency, or resource use shows the threshold is approaching.

For a capability that is optional, disabled, archived, or not yet offered, make urgency conditional on its activation unless enabling it is part of the current decision. The defect may be real while the work is not yet due.

Do not promote an issue merely because it is easy to fix. If implementation size is reasonably clear and helps ordering within a bucket, label it `small`, `medium`, or `large`; otherwise omit the estimate rather than guessing.

## 7. Report for action

Lead with a one-sentence release or product decision, followed by the assumed context.

Use action headings, not generic technical severity headings:

```text
## Fix before release
## Fix next
## Minor and polish issues
## Watch - do not fix yet
## Coverage and limits
## Ruled out
```

Omit empty headings. Use `Minor and polish issues` only when the selected finding threshold includes them. In blocker or important-only audits, do not enumerate filtered minor candidates or report how many were discarded unless asked. Include a `Watch` item only when it is a material, proven future risk with a concrete trigger worth monitoring.

Each issue should be understandable as one tracker-ready row:

> **Short outcome-focused title - action bucket.** Who encounters it and during which journey; what they observe; the proven cause or evidence in plain language; why it belongs in this bucket now.

Use real counts, timings, steps, or affected records when they strengthen the decision. Do not force a number into qualitative UX evidence. Keep code locations in compact evidence notes only when the audience needs them; do not make the user decode file names or implementation jargon to understand the issue.

When future risks are within the requested horizon, `Watch` items must say `Do not schedule yet` and include a trigger such as measured p95 latency, queue depth, data volume, tenant count, a planned feature, or a deployment change. They are risk-register entries, not active tickets. Omit the section when the user explicitly excludes future concerns.

`Coverage and limits` names the personas, journeys, features, states, devices or viewports, and boundaries actually tested, the evidence source, and any material area not tested. For every-feature coverage, summarize the coverage matrix and identify blocked or partial journeys. It must not imply whole-project certainty from sampled coverage.

`Ruled out` is limited to explicit hunches and a few high-value checks that were genuinely disproved. It is not padding.

Close with what is in good shape and the single highest-value action. Never recommend fixing everything before shipping.

## 8. Optional resolution planning

Enter this phase only when the user explicitly asks to resolve, design fixes for, or prepare implementation from an existing audit. Read [references/resolution-planning.md](references/resolution-planning.md).

Freeze the audit backlog before planning. Re-verify each finding against the current project, then work through one issue or genuinely coupled root-cause cluster at a time. Keep stable issue IDs and record the evidence, desired behavior, recommended approach, tradeoffs, touchpoints, dependencies, acceptance tests, and deferred boundaries in a resolution ledger. Do not describe a recommendation as approved until the user approves it.

Keep the target worktree unchanged. Put temporary probes, fixtures, scripts, logs, screenshots, caches, and test artifacts in an external disposable location; run in a disposable copy when the toolchain cannot avoid writing to the target. A user-requested resolution document is the only permitted planning output and should live outside the audited worktree by default.

After every selected issue has a disposition, consolidate the briefs into a dependency-ordered implementation handoff. Stop before implementation. Editing application code, tests, configuration, schemas, or data requires a separate implementation request. If audit and resolution planning were requested together, finish and freeze the audit before beginning this phase rather than mixing discovery with solution design.

## Stop conditions

Stop when all of the following are true:

- the promised primary journeys or risk boundaries were exercised;
- reportable candidates were verified to the evidence level appropriate for their impact and deduplicated; severe, ambiguous, and stateful claims received independent re-verification;
- remaining leads fall outside the selected lens or threshold; in all-reproducible mode, the promised feature and UI-state inventory has been covered;
- coverage limits are known and can be stated honestly.

Do not continue because the report looks short. A short report can be the strongest release signal.

## Anti-patterns

- Treating every confirmed issue as immediate work.
- Calling theoretical maximum impact "critical" without a plausible current trigger.
- Dispatching a fixed number of agents regardless of scope.
- Giving every agent the whole repository and the same open-ended prompt.
- Ending prompts with an unrestricted request for "any other issues."
- Auditing UI from source code or screenshots without completing user journeys.
- Treating one successful run as sufficient for a tool whose normal use requires revising inputs and comparing results.
- Using a full-page screenshot to judge workspace ergonomics without exercising the loop at a realistic viewport.
- Filtering minor UI findings after the user explicitly requested exhaustive UI or polish coverage.
- Reporting minor polish as a release blocker merely because it was requested.
- Using an accessibility-tree snapshot as proof of color, spacing, overlap, or other pixel-level behavior.
- Inheriting a browser tool's fixed issue target or source-inspection ban instead of following this audit's intent.
- Deep-reading the whole repository before deciding which current risks matter.
- Confusing missing best practice, dead code, or optimization opportunity with a user-facing defect.
- Fixing the project during an audit or resolution-planning phase.
