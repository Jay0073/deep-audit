# Audit resolution planning

Use this guide only after an audit backlog exists and the user explicitly asks to work out solutions, fixes, recommended approaches, or an implementation handoff. This is a design and verification phase, not an implementation phase.

## Preserve the phase boundary

The audited project remains read-only. Do not edit application code, tests, configuration, schemas, migrations, fixtures, snapshots, generated assets, or data while resolving findings.

- Read the target project and existing evidence in place.
- Put experimental scripts, fixtures, screenshots, traces, logs, caches, and other artifacts outside the target worktree in a disposable directory.
- If a test runner or build tool cannot avoid writing into the target, run it against a disposable copy or redirect every output to the external directory.
- Use read-only database access and bounded live probes. Do not mutate production systems or perform consequential external actions.
- Write a durable resolution ledger only when the user asks for one. Default to a location outside the audited worktree; if the user explicitly chooses a document inside it, that document is the sole permitted edit.

End this phase with an implementation handoff. Do not begin implementation until the user separately authorizes it.

## Freeze and normalize the backlog

Start from the existing audit rather than silently repeating a broad audit.

1. Give every finding a stable ID and retain original IDs from multiple audit layers.
2. Merge findings only when one root cause and one design genuinely resolve them; preserve all source IDs on the combined brief.
3. Record the selected scope and which findings the user wants resolved. Do not expand a blocker-only backlog into polish or future hardening.
4. Track each item with one status:
   - `Re-verification pending`
   - `Confirmed - solution needed`
   - `Recommendation ready`
   - `Approved`
   - `Partly fixed`
   - `Already fixed`
   - `Deferred with trigger`
   - `Ruled out`
5. Treat the audit as evidence, not timeless truth. Re-verify the current code and behavior before designing a fix.

New evidence may narrow, combine, defer, or rule out an old finding. Record that change instead of forcing every original item toward implementation. Add a newly discovered issue only when it is required to resolve the current finding safely; otherwise place it in a small follow-up list rather than reopening an unbounded audit.

## Work one decision at a time

The default interaction unit is one finding or one genuinely coupled cluster. Show the user the current brief and a lightweight progress count, not the full unresolved backlog in every response.

Before asking the user to choose, investigate all decisions that evidence can settle. Ask only for a real product, risk, cost, compatibility, or rollout tradeoff. If the user delegates judgment, record the result as `Recommendation ready`, not `Approved`. Mark it `Approved` only after explicit user agreement.

Design findings one by one, but do not assume they must later be implemented one by one. Preserve shared dependencies so implementation can happen in safe, coherent batches after the entire design pass is complete.

## Re-verify before prescribing

For the current finding:

1. Locate the current implementation and any related tests, runtime evidence, data contract, or deployment assumption.
2. Reproduce or re-derive the behavior with the cheapest decisive read-only evidence.
3. Check whether intervening changes already fixed it, changed its reach, or invalidated its priority.
4. State the current verdict and confidence. Distinguish runtime observation from code-only verification.
5. Stop if it is ruled out or already fixed; record the reason and move on without inventing work.

Keep probes proportional. A final multi-hour soak, production canary, migration rehearsal, or load run is a release gate for a frozen implementation—not a substitute for understanding or designing the fix.

## Write a solution brief

Each unresolved finding receives a compact but implementation-ready brief:

1. **Issue and current verdict:** Plain-language user or system consequence, present reach, status, and source IDs.
2. **Verified evidence and root cause:** What was observed now, how it was checked, relevant locations, and what changed since the audit.
3. **Desired behavior and invariants:** The outcome that must be true, including failure and recovery behavior. Avoid prescribing architecture before stating behavior.
4. **Options and tradeoffs:** Viable approaches considered, including operational cost, compatibility, migration, user impact, and complexity where relevant.
5. **Recommended approach:** The smallest durable design that resolves the current problem at the stated horizon. Say whether it is recommended or user-approved.
6. **Implementation touchpoints:** Components likely to change, contracts to preserve, dependencies, ordering constraints, migrations, and feature-flag or rollout needs. Do not edit them yet.
7. **Observability and rollback:** Signals, alerts, audit events, rollback path, and failure containment when the risk warrants them.
8. **Acceptance tests:** Specific happy-path, boundary, failure, recovery, regression, and user-journey checks that will prove the implementation. Include the final release gate separately.
9. **Boundaries and rejected shortcuts:** What this fix intentionally does not solve, what is deferred and until when, and approaches rejected with a short reason.

Do not pad every brief with irrelevant headings. Security, data migration, observability, rollback, and performance sections are conditional on the finding.

## Build the implementation handoff

After all selected findings have a disposition:

1. summarize counts by status and list any decisions still awaiting the user;
2. combine approved briefs into dependency-ordered implementation batches;
3. identify shared touchpoints and conflicts so separate fixes do not overwrite or contradict one another;
4. place prerequisites, migrations, compatibility work, and feature flags before their dependents;
5. attach acceptance tests to each batch and define cross-batch regression checks;
6. place destructive, production, soak, canary, or load verification last, after behavior and pass/fail criteria are frozen;
7. state rollback boundaries and which deferred triggers should reopen unscheduled work.

The handoff should let an implementation session act without rediscovering the problem or redesigning each solution. It does not authorize that session. Close by stating that the audited worktree was not changed and that implementation awaits a separate request.

## Anti-patterns

- Designing all findings in one large answer that leaves the user unable to make decisions.
- Treating every audit claim as still correct without re-verification.
- Marking an AI recommendation as agreed or approved without the user's decision.
- Editing code while the solution set is still changing.
- Running probes that leave artifacts or state in the audited worktree.
- Using a long live soak to discover the intended behavior or tune the design.
- Combining unrelated IDs merely to shorten the backlog.
- Expanding a current launch fix into speculative future architecture.
- Implementing each issue immediately and discovering shared dependencies only afterward.
