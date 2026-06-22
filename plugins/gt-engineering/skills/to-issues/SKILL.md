---
name: to-issues
description: Break a plan, spec, or PRD into independently-grabbable issues on the project issue tracker using tracer-bullet vertical slices.
disable-model-invocation: true
---

# To Issues

Break a plan into independently-grabbable issues using vertical slices (tracer bullets).

The issue tracker and triage label vocabulary should have been provided to you — run `/setup-gt-dev-skills` if not.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes an issue reference (issue number, URL, or path) as an argument, fetch it from the issue tracker and read its full body and comments.

**Check for wireframes.** If `docs/wireframes/` exists (produced by `/to-product-req`), read `wireframes.html` and `wireframes.md`. They are the design source of truth — the slices you draft must be wireframe-aware (see steps 3 and 4a).

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code. Issue titles and descriptions should use the project's domain glossary vocabulary, and respect ADRs in the area you're touching.

Look for opportunities to prefactor the code to make the implementation easier. "Make the change easy, then make the easy change."

### 3. Draft vertical slices

Break the plan into **tracer bullet** issues. Each issue is a thin vertical slice that cuts through ALL integration layers end-to-end, NOT a horizontal slice of one layer.

<vertical-slice-rules>

- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests)
- A completed slice is demoable or verifiable on its own
- Any prefactoring should be done first
- When wireframes exist, each UI-bearing slice maps to the wireframe screen(s) it builds

</vertical-slice-rules>

### 4a. Wireframe coverage check (when wireframes exist)

Before quizzing the user, reconcile the slices against the wireframes so the two don't drift:

- **Every wireframe screen must map to ≥1 issue.** Walk the screen list in `wireframes.html` / `wireframes.md` and confirm each is covered by a slice. Flag (or propose auto-creating) an issue for any screen with no coverage — this is what prevents the onboarding-style drift where a drawn screen has no ticket.
- **Explicitly note screens that are out of scope** rather than silently leaving them uncovered.

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each slice, show:

- **Title**: short descriptive name
- **Blocked by**: which other slices (if any) must complete first
- **User stories covered**: which user stories this addresses (if the source material has them)
- **Wireframe screen(s)**: which wireframe screen(s) this slice builds (if wireframes exist)

If wireframes exist, also surface the coverage check from step 4a: list any screens with no issue, and any screens deliberately out of scope.

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the dependency relationships correct?
- Should any slices be merged or split further?

Iterate until the user approves the breakdown.

### 5. Publish the issues to the issue tracker

For each approved slice, publish a new issue to the issue tracker. Use the issue body template below. These issues are considered ready for AFK agents, so publish them with the correct triage label unless instructed otherwise.

Publish issues in dependency order (blockers first) so you can reference real issue identifiers in the "Blocked by" field.

<issue-template>
## Parent

A reference to the parent issue on the issue tracker (if the source was an existing issue, otherwise omit this section).

## What to build

A concise description of this vertical slice. Describe the end-to-end behavior, not layer-by-layer implementation.

Avoid specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it here and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

## Design reference

Include this section only when wireframes exist and this slice has UI. Reference the relevant wireframe screen(s) by name/number, and embed or link the per-screen design spec from `wireframes.md` so the design intent (tokens, states, interactions) travels with the issue — not just the functional acceptance criteria. Omit this section entirely for slices with no UI.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Blocked by

- A reference to the blocking ticket (if any)

Or "None - can start immediately" if no blockers.

</issue-template>

Do NOT close or modify any parent issue.
