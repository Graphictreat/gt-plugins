# Issue tracker: GitHub

Issues and product requirements for this repo live as GitHub issues. Use the `gh` CLI for all operations.

## Conventions

- **Create an issue**: `gh issue create --title "..." --body "..."`. Use a heredoc for multi-line bodies.
- **Read an issue**: `gh issue view <number> --comments`, filtering comments by `jq` and also fetching labels.
- **List issues**: `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'` with appropriate `--label` and `--state` filters.
- **Comment on an issue**: `gh issue comment <number> --body "..."`
- **Apply / remove labels**: `gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **Close**: `gh issue close <number> --comment "..."`

Infer the repo from `git remote -v` — `gh` does this automatically when run inside a clone.

## When a skill says "publish to the issue tracker"

Create a GitHub issue.

## When a skill says "fetch the relevant ticket"

Run `gh issue view <number> --comments`.

## Keep the tracked issue in sync while implementing

The issue tracker is the shared source of truth. Any implementation skill that works against a tracked issue must keep it current — updating the issue should be as routine as committing, not a step the user has to ask for at the end.

- **On start:** `gh issue comment <number>` that work has begun, referencing the branch; move its triage label if the project uses one (e.g. `ready-for-agent` → `in-progress` via `gh issue edit`).
- **During:** after each meaningful slice / commit, comment a short progress note (what's done, what remains) referencing commit SHAs, and reference the issue in the commit message (`Refs #N`).
- **On finish:** comment a closing summary mapping commits → acceptance criteria, check off the criteria that are met, and `Closes #N` on the final commit. Only `gh issue close` when the acceptance criteria are _fully_ satisfied; be explicit about what was NOT done, and flag when closing commits live on an unmerged branch.