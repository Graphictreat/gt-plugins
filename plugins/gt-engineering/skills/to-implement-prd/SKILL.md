---
name: to-implement-prd
description: Implement a PRD end-to-end — fetch the PRD issue and its child issues, create a feature branch, implement each child in dependency order, and commit each with a consistent message format. Closes each child issue after its commit lands and writes an implementation summary. Slash-invoke only.
disable-model-invocation: true
---

# To Implement PRD

Take a PRD issue number, fetch the PRD and all of its child issues in dependency order, create a feature branch, implement each child issue one at a time, and commit each with a consistent message format. This is an AFK agent skill — it does the mechanical work of turning a decomposed PRD into a branch of commits.

The issue tracker and triage label vocabulary should have been provided to you — run `/setup-gt-dev-skills` if not.

## Invocation

```
/gt-engineering:to-implement-prd <PRD-issue-number>
```

If no PRD issue number is passed, list open issues whose title starts with `PRD:` (`gh issue list --search "PRD:" --state open`) and ask the user to pick one.

## Process

### 1. Fetch the PRD

Fetch the full body of the PRD issue from the issue tracker. Read its **Implementation Decisions** and **Testing Decisions** sections — these define the architecture, the seams, and what good tests look like. Everything you build must honour them.

Also read `CONTEXT.md`/ADRs (per `docs/agents/domain.md`) and, if `docs/wireframes/wireframes.md` exists, the design spec — they are the source of truth for domain vocabulary and UI intent.

### 2. Discover child issues

Find all open issues whose body contains a `## Parent` section referencing the PRD issue number (e.g. `#42`). These are the vertical slices created by `/to-issues`. Fetch each child issue's full body, including its `## What to build`, `## Acceptance criteria`, and `## Blocked by` sections.

### 3. Sort by dependency order

Read each child issue's `## Blocked by` section and topologically sort the children so every blocker is implemented before the issues it blocks.

- If a child's blocker is **another child in this PRD**, order it after that blocker.
- If a child's blocker is an issue that is **still open and not part of this run** (an external/unresolved blocker), **skip that child** and surface it in the final summary — do not implement it.
- If the dependency graph has a cycle, stop and report it rather than guessing an order.

### 4. Derive the PRD short name

Derive a short name from the PRD issue title for use in commit messages: strip the leading `PRD:` and any `Category: ` prefix pattern, then abbreviate to the first meaningful noun phrase.

- "CI/CD: Automate TestFlight distribution…" → `CI/CD`
- "Auth: Sign in with Apple flow" → `Auth`
- "Feed: Real-time review updates" → `Feed`

### 5. Create a feature branch

Create a feature branch named `feature/<slugified-prd-title>` from the current default branch (`main` or `master`). Slugify the PRD title (lowercase, hyphenated, prefix dropped). Confirm you are on a clean working tree before branching.

### 6. Implement each child issue in order

For each child issue, in dependency order:

a. Read the issue body — **What to build** (the end-to-end behaviour) and **Acceptance criteria** (the checklist).

b. Explore the codebase as needed to find the seams and prior art described in the PRD.

c. Implement the slice end-to-end. Prefer building it test-first — drive it through the `tdd` skill's red-green-refactor loop, testing on public interfaces per the PRD's Testing Decisions.

d. Verify the acceptance criteria are met — build, run the tests, or do a live check as appropriate. **A failing build or test means the slice is not done.** Do not commit a slice whose acceptance criteria are unmet; note the blocker in the summary and move on.

e. Commit with the message format below.

f. Close the child issue once its commit lands (`gh issue close <n>`), referencing the commit.

If an acceptance criterion requires **human action** (running a local command with credentials, uploading a signing key, setting a secret), implement everything around it but do not fabricate the credentialed step. Note exactly what the developer must do in the summary, and still commit the automatable part.

### 7. Commit message format

```
<PRD-SHORT-NAME>: <Issue title> (#<issue-number>)

One-paragraph body explaining the why and any non-obvious choices.

Co-Authored-By: <currently-running model> <noreply@anthropic.com>
```

The `Co-Authored-By` trailer names the model you are currently running as — no model selection or detection logic, just whatever model is executing this skill.

Example:

```
CI/CD: Fastlane signing infrastructure (#33)

Adds the Fastlane match configuration and lanes for code signing so
TestFlight builds can be produced without manual certificate juggling.
Stores nothing secret in the repo — the match passphrase is read from
the environment.

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
```

One commit per child issue. The subject line drops the `PRD:`/category prefix in favour of the derived short name, and always ends with the `(#<issue-number>)` reference.

### 8. Write an implementation summary

When all reachable child issues are done, write a short summary to the conversation:

- **Implemented** — each child issue, its commit subject, and a one-line note of what was built.
- **Skipped** — any child issue skipped because of an unresolved external blocker, with the blocking issue.
- **Manual follow-up** — any human-only steps the developer must do (secrets to set, keys to upload, local commands to run), and any acceptance criterion that could not be auto-verified.

## Guardrails

- **Never push to remote.** Leave that to the developer. Commit locally only.
- **Skip blocked children.** If a child issue's blocker is still open and outside this run, skip it and surface it in the summary — do not implement out of order.
- **Don't fake human-only steps.** If an acceptance criterion needs credentials or a manual upload, automate around it and note the gap; never invent the credentialed result.
- **Don't commit unverified slices.** A slice whose build fails or whose acceptance criteria are unmet is not committed — it goes in the summary as incomplete.
- Do NOT close or modify the PRD (parent) issue. Only close child issues after their commit lands.
