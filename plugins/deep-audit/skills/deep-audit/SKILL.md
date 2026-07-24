---
name: deep-audit
description: "Deep, whole-project issue-finding audit of one module, page, API, worker, pipeline, SQL script, or feature. Reads code end-to-end, verifies findings against real runtime data, diffs duplicate implementations, and returns plain-language issue rows ready to paste into a tracker. Covers correctness, data, UI/UX, reliability, and security across the whole system, not just the code. Use when the user asks to review/audit/analyse anything for bugs, gaps, or defects. Language and stack agnostic."
trigger: /deep-audit
---

# /deep-audit

Find real defects in ONE unit of work at a time, prove each one, and report them as plain sentences a non-engineer can read.

## Usage

```
/deep-audit <target>                      # audit one unit
/deep-audit <target> "<hunch>"            # seed it with what feels wrong
/deep-audit                               # ask the user what to audit
```

`<target>` is whatever unit the project uses: a page, an API surface, a background worker, a queue, a data pipeline, a SQL script, a set of migrations, a CLI command, a library module, a service.

---

## Prime directives

**Report only. Never fix anything.** No edits, no refactors, no "while I was here". Fixing is a separate request.

**Prove everything.** No "this might be", no "consider whether". If you cannot demonstrate it from the code or the data, either prove it or drop it. An audit that is 80% real and 20% speculation is worse than one with only the real findings, because the reader stops trusting all of it.

**Disprove out loud.** Actively try to kill your own hypotheses before reporting them. A finding you disproved is nearly as valuable as one you confirmed, because it stops someone spending a day on a non-problem. Report what you ruled out.

---

## Step 0 - Prerequisites, and asking

Establish these before doing anything. **If any are missing and you cannot determine them yourself, ask the user.** Do not guess and do not proceed half-blind.

1. **What is the target?** If not given, list what you can see and ask.
2. **Any hunches?** Ask: "Anything here that already feels wrong, or that you have doubts about?" A user hunch is the highest-value single input to an audit. If they have none, proceed anyway.
3. **Can real behaviour be observed?** Look for a dev database, seeded environment, fixtures, a runnable local process, logs, a staging endpoint, sample input files, or a way to execute the thing in a sandbox. Find connection details the way the project does (env file, config module, compose file, settings module). If you find a source but cannot reach it, ask rather than silently falling back to code-only.
4. **Orientation.** Read any CLAUDE.md, README, ARCHITECTURE, CONTRIBUTING, or docs index. Use it purely as a map. It will not contain the bugs.

---

## Step 1 - Classify the target

**This step decides which checklist you use. Do not skip it.** Most targets are one primary kind plus one or two secondary kinds.

| Kind | Looks like |
|---|---|
| **Interface** | page, screen, view, template, component tree |
| **Request handler** | HTTP route, RPC method, GraphQL resolver, webhook receiver, serverless function |
| **Async worker** | background job, queue consumer, cron task, scheduler, event handler |
| **Data pipeline** | ETL, batch aggregation, sync job, import/export, report generator |
| **Data layer** | SQL scripts, migrations, schema, stored procedures, query modules |
| **Executable** | CLI command, deployment script, one-off maintenance script |
| **Library** | reusable module, SDK, internal package, shared helper |

Then load the matching checklist from Step 3.

---

## Step 2 - Map the target

Assemble the full file set before reading anything deeply:

- The entry point (what triggers this)
- Everything it calls, down to the data or the outside world
- **Everything that WRITES the data this target reads.** This is where defects actually live. A thing showing wrong output is very often correct code reading badly-written data. Auditing the read path alone will miss the real cause.
- Configuration, environment, feature flags that change its behaviour
- Tests, if any
- Sibling implementations of the same logic elsewhere in the repo

Note the language and typing situation. Static types let you find dead exports and shape mismatches cheaply. Dynamic types need manual value tracing.

---

## Step 3 - Investigate in parallel

Split into 3-5 independent domains and dispatch one subagent per domain, all in a single message so they run concurrently. Choose domains that fit the target's kind - typically correctness, behaviour/UX or interface contract, lifecycle/state, reliability/failure, and security.

**How to write each subagent prompt.** This framing is the difference between a vague review and a sharp one:

- Give it **specific claims to confirm or deny**, not a general instruction to review. Turn every hunch, every suspicious comment, and everything you noticed while mapping into a numbered claim.
- Require a verdict per claim: **CONFIRMED / PARTIALLY CONFIRMED / NOT AN ISSUE**.
- Require `file:line` evidence for every claim. No evidence, no finding.
- Require a description of **actual current behaviour**, not just "this is wrong".
- Require it to say **what it tried to prove and could not**.
- Always end with: **"While reading, report any OTHER defects you find that are not in this list."** This trailing clause reliably produces a large share of the best findings.
- Tell it to **read the primary files completely**. Grep only finds what you already suspected.
- Require the symptom in plain language, with the technical cause stated separately.

**Verify your subagents.** Take each headline number or severe claim an agent returns and re-derive it yourself before trusting it. Agents are confident and occasionally wrong. This costs minutes and is what makes the final report trustworthy.

### Universal checklist - apply to every target

- **Boundaries:** zero, one, empty, null, missing, maximum, negative, duplicate, unicode, very large.
- **Error paths:** what happens when the dependency fails? Is the failure visible, swallowed, or disguised as a normal empty result?
- **Duplicate implementations:** find every place the same rule or metric is computed and diff them line by line. Any difference is a defect in at least one. Check specifically for different windows, filters, sources, rounding, null handling, and one path cached while another is live.
- **Units and scales:** is a 0-100 value rendered on a 0-1 scale? Is a raw count driving a percentage? Do two different quantities share one label, axis, or column?
- **Caps and truncation:** when a set is limited to top N, was the aggregate computed before or after the cut? Does the ranking use the same measure that is displayed?
- **Time windows:** are all parts of the feature using the same window? Is it anchored consistently?
- **Comments as assertions:** comments claiming "always", "never", "mirrors X exactly", "guaranteed" are testable. Test them. A false intent comment is both a defect and a signpost to a divergence someone forgot to sync.
- **Dead code:** an export nothing imports, state written but never read, a handler never called. Each usually means a feature was half-built or silently dropped. Find what it was meant to do.

### Interface

Empty, loading, and error states all present and distinguishable. State reset when the active entity changes (account, project, tenant, brand). Filters, counts, and badges consistent with each other and with what is displayed. Labels matching what is actually computed. Keyboard operability and focus-visible states on every interactive element. Semantic roles on things that behave like buttons, dialogs, checkboxes. Responsive behaviour - what is hidden at small sizes and is anything offered in its place. Styles targeting selectors that no longer exist in the markup. Races from rapid interaction (stale responses overwriting newer ones). Long or hostile content overflowing.

### Request handler

Authorization on **every** entry point, not just the obvious ones - check each handler individually, since gaps cluster in older or less-used routes. Tenant and ownership isolation on both reads and writes. Input validation: malformed identifiers should return a clean not-found, not a server error, and should not flood logs. Unbounded numeric parameters (page size, limit, day range, depth) that let one request consume unbounded memory, time, or storage. Injection at every sink the input reaches - query, shell, path, template, response header, log. Output encoding for the consumer that will actually open it. Result sets with no limit. N+1 queries. Idempotency on anything that writes. Rate limiting on anything expensive.

### Async worker

Retry classification: are permanent failures (bad credentials, billing, quota exhausted, malformed payload) distinguished from transient ones, or does everything go through the same backoff? **Compute the actual worst-case retry duration in wall-clock time and state it.** Timeouts on every outbound call, including the ones the SDK sets by default - find that default and multiply it by the SDK's own internal retries. Terminal-state reconciliation: when a job dies, does its associated record get updated, or is it orphaned? Check every path to death, especially early returns that skip the cleanup. Idempotency and duplicate execution - what happens if the same work is triggered twice. Concurrency guards, and whether they are enforced atomically or computed outside the transaction that acts on them. Multi-instance safety: if more than one copy runs, do they race, and are stale-recovery thresholds longer than the longest legitimate run? Graceful shutdown and draining. Dead-letter handling and whether anything ever cleans up. Head-of-line blocking from batched waits. Errors swallowed into a success status.

### Data pipeline

Deduplication at the point of write, not just at read. Ordering assumptions that the source does not guarantee. Partial-failure semantics - does a mid-run failure leave a half-written result that looks complete? Window and watermark correctness, including boundary records. Divergence between the incremental path and the backfill path. Schema drift and silent type coercion. Aggregate correctness: recompute the output from the source yourself and compare. Records silently dropped by a filter or a join.

### Data layer

Indexes that actually serve the queries being run - check the predicate shape, not just the column name, since a function or expression in the predicate makes a plain index unusable. Foreign key columns without indexes (most engines do not create these automatically). Transaction scope and lock duration. Destructive or irreversible operations without a guard. Migration reversibility and what the down path destroys. NULL semantics in comparisons and aggregates. Integer division, overflow, and rounding. Timezone and date-boundary handling. Recompute any aggregate the code claims and compare it to the real answer.

### Executable

Argument validation and clear failure on bad input. Correct exit codes. Destructive actions without confirmation or dry-run. Path handling, including spaces and traversal. Partial-run state: what is left behind if it dies halfway. Secrets appearing in arguments, logs, or output. stdout versus stderr discipline for anything that will be piped.

### Library

Public surface matching its documentation. Error types that callers can actually discriminate. Resource cleanup on both success and failure paths. Concurrency and reentrancy assumptions. Default values that are wrong for common use. Breaking changes to a published interface.

---

## Step 4 - Techniques that find what reading misses

Apply these deliberately. Each has independently produced findings that a straight read-through did not.

**Observe real behaviour.** Do not stop at reading code. Adapt to the target: query the database and recompute the aggregate yourself; call the endpoint; run the worker against a test payload; inspect the job or queue tables for actual outcomes and stuck records; run the query plan; execute the script in a sandbox; compare a pipeline's input count to its output count. This is what converts "the total may not sum correctly" into "it sums to 53%, and here are the 22 rows being hidden." Do it for every number, rate, count, and percentage the target claims.

Read-only always. If you must write, wrap it in a transaction you roll back. **Put scratch scripts in a scratch/temp directory, never in the repo.** If a helper must sit inside the project for module resolution, delete it and verify the working tree before you report.

**Diff duplicate implementations.** See the universal checklist. This is the single most productive technique in codebases that have grown by accretion.

**Git archaeology on divergences.** When two paths disagree, run `git log -S "<identifier>"` on both. Very often a fix landed on one and never on the other. That turns "these disagree" into "here is exactly when and why they diverged", which is what makes it actionable.

**Follow state across boundaries.** Reload, navigate away and back, switch the active entity, restart the process, run two copies. State that fails to reset or reconcile across a boundary is a recurring and very confusing class of defect.

**Check what the data says about the code.** Sort by frequency and look at the top rows. Anomalies surface immediately: a redirect wrapper ranking as a top publisher, one source producing 70% of all records, 88% of a field being a duplicate of another field. These are invisible in code and obvious in data.

**Question the semantics, not just the arithmetic.** A calculation can be perfectly correct and still measure the wrong thing. Ask what each label claims, then check whether the computation actually delivers that claim. Look at real rows and ask whether a human would agree with how they were classified.

---

## Step 5 - Verify before reporting

Drop anything you cannot substantiate. For each surviving finding confirm:

- The evidence actually says what the finding claims
- The symptom is something a real user or operator could encounter, described concretely
- It is not a duplicate of another finding
- It is a defect, not a style preference

If several findings share a root cause, report the cause once and note what it affects.

---

## Step 6 - Output

Plain rows, ready to paste into a tracker. **One issue per row. Plain English. No file paths, no line numbers, no jargon.**

Each row: what someone would observe, then the cause in plain terms, then a concrete number that proves it.

Group by severity:

```
### Wrong data
### Broken behaviour
### Missing functionality
### Interface and accessibility        (only if relevant to the target)
### Security
### Found outside <target>             (only if you strayed - see below)
### Ruled out
```

**Found outside the target.** Following a thread out of the audited unit is allowed and often valuable, but report those findings under their own heading so the reader knows the boundary moved.

**Ruled out.** List the plausible defects you specifically checked and disproved, with the evidence. Two or three lines. This is not padding - it stops someone re-investigating the same dead ends.

Style rules:

- Symptom first, cause second.
- Include real numbers whenever you have them. "Currently 81 runs stuck since 26 June" beats "some runs get stuck".
- No file names, function names, line numbers, or library names in the rows.
- No fix instructions. Describe the problem. One closing sentence is acceptable if the fix is genuinely obvious; never a plan.
- One to three sentences per row. If it needs more, it is probably two issues.
- Do not pad. Twelve real issues beat forty with filler.
- Never use em-dashes. Use a hyphen with spaces.

Close with two or three sentences: what is in good shape, and which single fix delivers the most value.

---

## Anti-patterns

- **Auditing several targets at once.** Depth collapses. One per run.
- **Grepping instead of reading.** You will only confirm what you already suspected.
- **Reporting hypotheses.** If you cannot prove it, do not write it.
- **Skipping the observation step** because reading felt sufficient. It is not. The strongest findings come from comparing claimed behaviour to actual behaviour.
- **Trusting a subagent's headline number** without re-deriving it.
- **Auditing only the read path** when the defect is in what wrote the data.
- **Using the wrong checklist** because you skipped classification.
- **Fixing things.** This skill reports.
- **Lint-level padding.** Naming, formatting, and import order are not findings unless they cause a real defect.
- **Reporting dead code by itself.** Report it when it has a live effect, or when it reveals a dropped feature.
