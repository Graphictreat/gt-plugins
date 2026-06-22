---
name: validate-prd-implementation
description: Validate the current implementation against a PRD in GitHub issues — checks user stories, implementation decisions, testing decisions, wireframes, and child issue acceptance criteria. Posts a gap report to the PRD issue and creates new issues for anything missing.
disable-model-invocation: true
---

# Validate PRD Implementation

Compare the codebase on the default branch against a PRD issue to surface gaps, unimplemented requirements, and bugs. Post the findings as a structured comment on the PRD issue and create vertical-slice issues for anything missing.

The issue tracker and triage label vocabulary should have been provided to you — run `/setup-gt-dev-skills` if not.

## Process

### 1. Pick a PRD issue

List all open GitHub issues whose title starts with `PRD:` using `gh issue list --search "PRD:" --state open`. Present the list to the user and ask them to pick one. Fetch the full body of the selected issue.

### 2. Fetch child issues

Search for all issues whose body contains a `## Parent` section referencing the PRD issue number (e.g. `#42`). These are the vertical slices created by `/to-issues`. Fetch each child issue's full body, including its `## Acceptance criteria` checklist.

### 3. Switch to the default branch

Check out the default branch (`main` or `master`) before reading any code or running tests. All validation is against what is actually on the default branch — not the current working branch.

### 4. Run the test suite

Run the project's test suite. Collect the full pass/fail output. A failing test is objective evidence that an acceptance criterion is not met — treat it as a confirmed gap regardless of what the code analysis says.

### 5. Check for wireframes

If `docs/wireframes/wireframes.md` exists, read it. List the screens it defines. Each screen must be accounted for — either covered by an existing child issue or flagged as missing coverage.

### 6. Analyse the codebase

Read the codebase to understand what is actually implemented. Focus on the seams described in the PRD's Implementation Decisions section. Do not trust comments or TODOs — only shipped, callable code counts as implemented.

For each PRD section, judge coverage:

- **User Stories** — Is there code that satisfies each numbered story? Mark each as ✅ Covered, ⚠️ Partially covered, or ❌ Missing.
- **Implementation Decisions** — Is each decision reflected in the code (module exists, interface matches, schema applied, API contract honoured)?
- **Testing Decisions** — Does the test suite cover the modules and seams listed? Do passing tests exercise the described behaviors?
- **Wireframe screens** — Does each screen in `wireframes.md` have a corresponding UI implementation? Flag any screen with no code coverage.
- **Child issue acceptance criteria** — For each `- [ ]` checkbox in a child issue: is the criterion met by the code and/or passing tests? For each `- [x]` checkbox: does the code and tests actually confirm it?

### 7. Produce the gap report

Post a comment on the PRD issue with the following structure:

```
## Implementation Validation Report

**Summary**: X/Y user stories covered · Z acceptance criteria unmet · N bugs found · M wireframe screens missing

---

### User Stories

For each user story, one line:
✅ / ⚠️ / ❌ — Story N: <story text> — <one-line finding>

---

### Implementation Decisions

For each decision, one line:
✅ / ⚠️ / ❌ — <decision summary> — <one-line finding>

---

### Testing Decisions

For each testing decision, one line:
✅ / ⚠️ / ❌ — <decision summary> — <one-line finding>

---

### Wireframe Screens

For each screen in wireframes.md, one line:
✅ / ⚠️ / ❌ — <screen name> — <one-line finding>

(Omit this section entirely if docs/wireframes/ does not exist.)

---

### Child Issue Acceptance Criteria

For each child issue, a subsection:
**#N — <issue title>**
- ✅ / ⚠️ / ❌ Criterion text — <finding if not passing>

Note any checkbox state mismatches (ticked but not implemented; unticked but implemented).

---

### Issues Created

Links to any new issues created as a result of this validation (see step 8).
(Omit this section if no new issues were created.)
```

### 8. Create issues for gaps and bugs

For each confirmed gap (missing user story, unmet acceptance criterion, unimplemented decision) and each bug found:

- Create a new GitHub issue using the vertical-slice template below
- Label it `ready-for-agent`
- Reference the PRD issue in the `## Parent` section so future runs of this skill pick it up
- Link each new issue in the "Issues Created" section of the report comment

<issue-template>
## Parent

#N — <PRD issue title>

## What to build

A concise description of the gap or bug. For gaps: describe the missing end-to-end behavior. For bugs: describe the observed behavior, the expected behavior, and the evidence (failing test name or code path).

Avoid specific file paths or code snippets unless they encode a decision more precisely than prose can.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Blocked by

None — can start immediately.
</issue-template>

Do NOT close or modify any existing issues. Do NOT tick or untick checkboxes in child issues — report discrepancies only.
