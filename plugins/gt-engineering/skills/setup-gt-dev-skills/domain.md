# Domain docs

Some skills need to learn this repo's domain language and past architectural decisions before they act. This file records **where those docs live** and **the rules for reading them**.

## Layout

This repo is **single-context** (the default — most repos are this):

- `CONTEXT.md` at the repo root — the project's domain language, key concepts, and conventions.
- `docs/adr/` at the repo root — Architectural Decision Records, one file per decision.

> If this repo is instead **multi-context** (typically a monorepo), replace the section above with: a `CONTEXT-MAP.md` at the root that points to per-context `CONTEXT.md` files (e.g. `frontend/CONTEXT.md`, `backend/CONTEXT.md`), each with its own `docs/adr/` alongside it.

## Consumer rules

When a skill needs to understand the domain:

- **For domain language and concepts** — read `CONTEXT.md` before reasoning about the code, so names and concepts are used the way this project uses them. In a multi-context repo, start at `CONTEXT-MAP.md` and read the `CONTEXT.md` for the area being worked on.
- **For past architectural decisions** — read the relevant records in `docs/adr/` before proposing or changing architecture, so prior trade-offs are respected rather than re-litigated.
- **When a doc is missing** — proceed without it, but say so. Don't invent domain terms or assume a decision was made; treat absence as "not yet documented".
- **When work changes the domain** — if a change introduces a new concept or supersedes a recorded decision, note that the corresponding doc should be updated (or add a new ADR), rather than letting the docs drift.
