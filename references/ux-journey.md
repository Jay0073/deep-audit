# UX and UI journey audit

Use this guide for UX, UI, usability, page, browser-test, and end-to-end feature requests. The primary unit of analysis is a user journey. Pages, components, and visual details are states within that journey.

## Honor the requested intent

Read the lens, coverage, and finding threshold selected in `SKILL.md` before testing.

- **Release blockers:** Concentrate on whether important users can safely complete their main goals.
- **Important issues:** Include meaningful friction, confusion, recovery, trust, accessibility, and state problems; omit inconsequential polish.
- **All reproducible issues:** Include verified minor spacing, alignment, color, hover, animation, consistency, responsive, and visual-polish defects within scope. Label them minor; do not inflate their urgency.

An explicit request for deep UI detail or every minor issue overrides the normal polish filter. An explicit request for major or important UX issues keeps that filter. "All UI issues" expands UI coverage and reporting; it does not silently expand into unrelated backend hardening.

Pure taste is not an objective defect. If the user explicitly asks for design judgment, report subjective observations as `Design judgment` and state the reference used: a design system, product intent, established pattern, or clearly identified usability principle.

## Build the feature-to-journey map

Identify current personas and what each came to accomplish. Prefer evidence in this order:

1. The user's stated personas, launch goal, scope, and hunches.
2. Real analytics, support reports, session evidence, or observed workflows.
3. Product requirements and current navigation, routes, and available roles.
4. A clearly stated inference from the runnable product when better evidence is unavailable.

For each selected feature, record:

- persona and realistic starting state;
- entry point and how the user discovers it;
- goal and visible success condition;
- action sequence and connected features crossed along the way;
- underlying requests, data writes, jobs, or outside services;
- likely mistakes, interruptions, and recovery path;
- whether normal use is one-shot or iterative and, if iterative, the repeated working loop;
- relevant viewport, input method, role, account, or tenant state;
- coverage status: `tested end to end`, `partially tested`, `blocked`, or `not tested`.

Connect features into real workflows. Do not test sign-up, onboarding, creation, sharing, payment, export, or administration only as isolated screens when users naturally move between them.

When the user asks for important issues, rank journeys by current frequency, user value, and business importance. When the user asks for every feature, inventory every reachable feature first and preserve honest coverage statuses rather than sampling a few pages and calling the app tested.

## Test the operating loop, not only first completion

Classify each important journey before testing it:

- **One-shot:** the user normally submits once and leaves, such as confirming an email or downloading a known file.
- **Iterative:** the user expects to adjust, inspect, compare, and adjust again, such as a calculator, dashboard, editor, search/filter view, configurator, or analysis tool.

For an iterative journey, identify its **working set**: the controls, context, output, comparison state, and actions the user needs during one decision loop. After producing the first valid result:

1. keep the browser at a representative laptop or device viewport rather than relying on a full-page capture;
2. inspect whether the working set is visible together or connected by an obvious, low-friction movement;
3. change one meaningful input and verify that the output updates correctly;
4. judge the travel required between editing and evaluating, including repeated scrolling, focus loss, sticky regions, layout shifts, and obscured content;
5. check whether the previous value or result remains understandable when comparison is part of the task;
6. repeat once more when the tool is chiefly used for exploration or comparison.

Use human workflow questions: Can the user keep their place? Can they see the consequence while making the next decision? Must they remember information that could remain visible? Does ordinary repetition become tiring or error-prone? Is the most-used action visually and spatially close to its feedback?

Scrolling alone is not a defect. Report it when the layout causes repeated travel, hides needed context, breaks comparison, moves the user's target, or otherwise adds material friction to a frequent core loop. A long page can work well; a short page can still have poor input-result continuity.

## Choose a real browser executor

Use an actual browser when the app is runnable. Choose the executor that best fits the project and requested evidence:

- Reuse an existing Playwright or equivalent test setup when available.
- Prefer a cross-browser runner when browser differences are in scope.
- Use an agent-oriented browser CLI or MCP for efficient exploratory testing when it can interact, capture screenshots, inspect console/network activity, and preserve sessions.
- Use vision or coordinate interaction for canvas, maps, charts, image editors, and controls absent from the accessibility tree.

The executor does not determine validity; the evidence does. Accessibility-tree snapshots are good for roles, names, text, states, and stable interaction. They cannot prove color, spacing, overlap, clipping, or visual hierarchy. Screenshots can prove static visual states but not an interaction sequence. Source inspection can explain a cause but cannot by itself prove what a user experienced.

Do not inherit a generic browser workflow's fixed issue count, severity model, source-inspection policy, or page-visit requirement independently of the requested coverage. Use its browser capabilities under this audit's intent and stop conditions.

## Run paired journey and logic passes

For important interactive features, preserve two perspectives when logic, persistence, permissions, or cross-feature state is in scope. Skip the white-box pass for a purely visual or polish audit unless underlying state affects the visual claim.

1. **Black-box journey pass:** Start where the persona starts and use only cues visible in the product. Do not use source knowledge to skip discovery or assume intent.
2. **White-box logic pass:** Trace the same actions through requests, validation, state transitions, data writes, background work, and responses.
3. **Reconciliation:** Compare what the interface claims with what was actually stored, calculated, sent, or authorized.

These passes may be performed by separate agents when delegation is available, or sequentially by one agent. Keep their initial observations independent before reconciling them.

## Browser-first method

Use realistic seeded or disposable data. Do not merely inspect screenshots, markup, or individual controls.

For each selected journey:

1. Define the persona, starting state, goal, and visible success condition.
2. Start where that user realistically starts, not at a deep link unless that is normal behavior.
3. Discover and complete the journey using only interface cues.
4. Observe comprehension, defaults, validation, loading, progress, feedback, completion, and persistence.
5. If the journey is iterative, revise a meaningful input after the first result and evaluate the complete working loop at the normal viewport.
6. When logic or persistence is in scope, verify the underlying saved or computed outcome when access is available.
7. Exercise the most relevant state boundaries: refresh, back/forward, retry, double action, cancel, resume, account or tenant switch, session expiry, and representative viewport changes.
8. Exercise likely failures using safe test data or controlled network/dependency behavior and check whether recovery is understandable.
9. Reproduce each reported issue and capture evidence appropriate to the claim.

Do not perform real purchases, send messages, delete live data, or cause other external effects without authorization.

## What deserves attention

Evaluate the journey in this order, while applying the selected finding threshold:

1. **Task completion:** Can the intended user discover, start, and finish the job?
2. **Correctness and trust:** Does the interface show the right entity, amount, status, scope, and consequence? Does it claim success before success exists?
3. **In-page operating continuity:** In an iterative tool, can the user revise inputs, understand the updated result, and compare outcomes without losing place or carrying avoidable information in memory?
4. **Cross-feature continuity:** Does output from one feature become the correct starting state for the next? Are permissions, selections, drafts, and context preserved?
5. **Errors and recovery:** Are likely mistakes prevented or explained, is entered work preserved, and can the user recover without guessing?
6. **System status and state:** Are loading, saving, empty, disabled, pending, complete, and failed states distinct? Does state stay correct across navigation and identity changes?
7. **Comprehension and decisions:** Are labels, hierarchy, defaults, and next actions clear enough for the intended user to choose correctly?
8. **Accessibility and device use:** Can the journey be completed with keyboard and meaningful semantics, visible focus, readable contrast, zoom, and representative screen sizes?
9. **Visual consistency and polish:** Spacing, alignment, typography, color, hover/focus/pressed states, animation, clipping, density, and component consistency. Report all reproducible items when requested; otherwise require meaningful present-day consequence.

Accessibility is not corner polish when it prevents a current user from completing a journey. Prioritize it by affected journey and impact.

## Match evidence to the claim

- **Interactive or state failure:** Reproduce through actual browser actions. Capture concise steps plus a trace, video, or before/action/after screenshots when useful.
- **Iterative workspace friction:** Capture the first result and at least one input revision at the representative viewport. Show the travel, lost context, obscured working set, or layout movement; a full-page screenshot alone is insufficient.
- **Static visible defect:** A screenshot at the relevant viewport and state can be sufficient for clipping, overlap, misalignment, typography, or color.
- **Semantic or keyboard defect:** Use the accessibility tree, focus sequence, keyboard interaction, and visible focus evidence. An automated scan alone is not a complete accessibility judgment.
- **Data or logic mismatch:** Capture the visible result and independently verify the request, record, event, or calculation.
- **Code-only suspicion:** Keep it as a hypothesis until runtime proof is impossible; then label it `verified from code, not observed in the browser` and do not describe it as lived UX.
- **Design judgment:** Show the relevant state and name the design reference or principle. Do not present an unsupported preference as fact.

Before reporting an observation, record the state, viewport, persona, and shortest reproduction. Independently re-verify severe, ambiguous, intermittent, or stateful claims. For a low-risk static or polish issue, one repeat in the same browser pass plus a clear screenshot is sufficient. Minor findings still need evidence; exhaustive does not mean speculative.

## Known limits

State relevant limitations instead of filling them with assumptions:

- Without analytics or research, journey frequency and real-user comprehension are inferred.
- Browser/device emulation does not fully reproduce physical hardware, operating-system rendering, or every assistive technology.
- Automated accessibility checks do not establish complete accessibility.
- Missing credentials, roles, test data, third-party sandboxes, or a runnable environment can block complete flows.
- No finite automation pass proves every latent combination of state; exhaustive means every inventoried reachable feature and promised state was attempted, with gaps disclosed.

## UX coverage note

Name the personas, connected journeys, features, devices or viewports, input methods, and states actually tested. Distinguish end-to-end journeys from partially tested features and static page samples. List blocked and untested areas. If analytics were unavailable, say journey priority was inferred rather than observed.
