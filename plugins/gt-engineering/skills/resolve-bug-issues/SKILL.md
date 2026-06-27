---
name: resolve-bug-issues
description: Resolve every open bug issue in parallel — fan out one subagent per `bug`-labelled issue, each fixing its issue test-first on a dedicated branch and opening a PR, then coordinate by merging green PRs in dependency order and reporting an issue → branch → PR → status table. Slash-invoke only.
disable-model-invocation: true
---

# Resolve Bug Issues

Read all open `bug`-labelled issues, fan out one parallel subagent per issue to fix it on a dedicated branch (test-first, one PR each), then act as coordinator: review the PRs, merge the green ones in dependency order, and report a summary table. This is an AFK swarm skill — it clears a bug backlog in one supervised batch instead of one issue at a time.

The issue tracker and triage label vocabulary should have been provided to you — run `/setup-gt-dev-skills` if not.

## Invocation

```
/gt-engineering:resolve-bug-issues [label]
```

`label` is optional and defaults to `bug` — pass an override if this repo tracks bugs under a different label (per `docs/agents/triage-labels.md`).

## Process

### 1. Discover bug issues

Issues are identified **by the `bug` label only** — not by any title prefix or naming convention. List them with `gh issue list --label bug --state open --json number,title,body`. Fetch each one's full body (the reproduction steps, expected vs. actual behaviour, and any `## Blocked by` section). If there are none, say so and stop.

### 2. Determine dependency order

Read each issue's `## Blocked by` section and build a dependency graph.

- Issues with no blocker among this set are **independent** — they run fully in parallel.
- An issue blocked by another issue in this set must have its blocker's PR **merged first** before its own PR merges.
- If the graph has a cycle, stop and report it rather than guessing an order.

### 3. Fan out one subagent per issue

Spawn a parallel subagent for each issue, **each in its own isolated git worktree** so concurrent file edits don't collide. Give each subagent its issue number, title, and full body, plus these instructions:

- **Create a dedicated branch** `bugfix/<issue-number>` off the current default branch. **NEVER commit to main.**
- **Reproduce the bug with a failing test first** (RED) — drive it through the `tdd` skill. The test must fail for the right reason before any fix, proving it actually captures the bug.
- **Implement the minimal fix** (GREEN). No unrequested features, abstractions, or drive-by refactors — fix the bug and nothing else.
- **Confirm the full test suite and the build pass** — not just the new test.
- **Commit** with a clear message, then **open a PR** whose body contains `Closes #<issue-number>` so the issue auto-closes on merge.
- **Return**: issue number, branch name, PR URL, and pass/fail status — plus a note if the bug could not be reproduced or fixed.

If a subagent cannot reproduce or fix its bug, it should leave the PR as a **draft** (or open none) and report why, rather than opening a green-looking PR that doesn't actually fix anything.

### 4. Coordinate the merges

Once the subagents return, act as coordinator. Walk the PRs in dependency order (blockers first):

- Review the PR diff and its test/build/CI status.
- **Merge only green PRs** (`gh pr merge <n> --merge`). Skip any with failing checks, an unreproduced bug, or unresolved review concerns — surface them in the summary instead of merging.
- Never merge a blocked PR before its blocker's PR has merged.
- Each merged PR auto-closes its issue via `Closes #<n>`; verify the issue closed and close it manually if the auto-close didn't fire.

### 5. Report the summary table

Give the user a markdown table plus any follow-up notes:

```
| Issue | Branch | PR | Status |
| --- | --- | --- | --- |
| #12 — <title> | bugfix/12 | #41 | ✅ merged · issue closed |
| #15 — <title> | bugfix/15 | #42 | ⏳ open — needs human review |
| #18 — <title> | bugfix/18 | — | ⚠️ could not reproduce |
| #20 — <title> | bugfix/20 | #43 | ⛔ skipped — blocked by #15 |
```

Note any human follow-up required (a PR left open for review, a bug that couldn't be reproduced, a blocked issue awaiting its blocker).

## Guardrails

- **Never commit or push to main.** Every change goes through a dedicated branch and a PR.
- **One branch and one PR per issue** — no batching unrelated fixes together.
- **Each parallel subagent works in its own isolated worktree** to avoid clobbering another agent's working tree.
- **Don't merge a PR with failing tests/build, an unreproduced bug, or unresolved review concerns** — surface it instead.
- **Respect dependency order** — never merge a blocked PR before its blocker.
- **Keep fixes minimal** — reproduce, fix, verify. No speculative features or unrelated refactors.
- If a bug can't be reproduced or fixed, leave its PR as a draft (or open none) and surface it in the summary rather than faking a fix.
