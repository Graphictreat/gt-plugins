# graphictreat/claude-plugins

Private Claude Code plugin marketplace for the Graphictreat team. Hosts the `gt-core` plugin (shared team-wide skills), `gt-engineering` (per-repo engineering workflow), and `gt-social` (social media content generation).

This repo is **private**. Access requires membership in the `graphictreat` GitHub org.

## What's in here

```
.
├── .claude-plugin/
│   └── marketplace.json       Marketplace catalog — lists available plugins
├── plugins/
│   ├── gt-core/
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json    Plugin manifest (name, version, author)
│   │   └── skills/
│   │       ├── fan-out-fan-in/SKILL.md
│   │       ├── grill-me/SKILL.md
│   │       ├── model-chat/SKILL.md
│   │       ├── pipeline/SKILL.md
│   │       └── stochastic-consensus/SKILL.md
│   ├── gt-engineering/
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   └── skills/
│   │       └── setup-gt-dev-skills/
│   │           ├── SKILL.md
│   │           ├── issue-tracker-github.md
│   │           ├── triage-labels.md
│   │           └── domain.md
│   └── gt-social/
│       ├── .claude-plugin/
│       │   └── plugin.json
│       └── skills/
│           └── x-post-gen/SKILL.md
└── README.md
```

## Available skills

<details>
<summary><strong>gt-core</strong> — shared team-wide skills</summary>

| Skill | Description | Best used for |
| --- | --- | --- |
| `fan-out-fan-in` | Spawns N cheap parallel researcher subagents on different angles of a question, then synthesizes their outputs with one stronger model. | Open-ended research where breadth + speed beat serial depth — "research X", "find best Y", "compare A/B/C", "how should I optimize Z". |
| `stochastic-consensus` | Spawns N agents in parallel with slightly different personas, each produces M independent ideas, then aggregates by frequency — high-vote ideas are consensus, single-vote ideas are outliers. | Brainstorming, option generation, ranking, and strategic analysis where you want to filter hallucinations and surface the full search space. |
| `pipeline` | Sequential specialist handoff — decomposes a task into stages and runs each as a focused subagent with its own clean context (dev → review → test, research → design → implement → QA). | Multi-phase tasks where carrying all prior context into the next phase would degrade quality — "build then test", "design and review". |
| `model-chat` | Multi-agent debate / shared-room — N agents iterate over R rounds; each round every agent sees all prior responses and refines, challenges, or extends. | Letting ideas evolve under peer pressure rather than just being aggregated — "debate", "discuss", "round-table", "have the models argue". |
| `grill-me` | Interviews the user relentlessly about a plan or design, walking down each branch of the decision tree and resolving dependencies one-by-one. Provides a recommended answer with every question. | Stress-testing a plan or design before implementation — "grill me on this", "poke holes in my approach", aligning on architecture before coding. |

</details>

<details>
<summary><strong>gt-engineering</strong> — per-repo engineering workflow</summary>

| Skill | Description | Best used for |
| --- | --- | --- |
| `setup-gt-dev-skills` | Scaffolds an `## Agent skills` block in `AGENTS.md`/`CLAUDE.md` plus `docs/agents/` so engineering skills know the repo's issue tracker (GitHub, or another tracker you describe), triage label vocabulary, and domain doc layout. Slash-invoke only (not auto-triggered). | Bootstrapping a fresh repo to be consumable by the downstream engineering skills, or refreshing those configs when the issue tracker / label vocab changes. |

</details>

<details>
<summary><strong>gt-social</strong> — social media content generation</summary>

| Skill | Description | Best used for |
| --- | --- | --- |
| `x-post-gen` | Generates a high-engagement, educational X (Twitter) post via a sequential pipeline (brief → draft → playbook critique → polish), encoding a 2026 X playbook with market-specific overlays. | Writing an X post or thread optimized for the algorithm; takes a topic + optional market (b2b-saas / indie / creator / crypto / science / generic) + format (single / thread). |

</details>

## Installation

Run these once on each machine.

### 1. Make sure you can read this repo

You need a GitHub account that's been added to the `graphictreat` org with read access. Verify:

```bash
gh auth status
gh repo view graphictreat/claude-plugins
```

If `gh` isn't set up, run `gh auth login` first.

### 2. Add the marketplace

Inside Claude Code:

```
/plugin marketplace add graphictreat/claude-plugins
```

Or from the shell:

```bash
claude plugin marketplace add graphictreat/claude-plugins
```

This clones the marketplace into `~/.claude/plugins/marketplaces/graphictreat/` using your `gh` credentials.

### 3. Install the plugins you want

Inside Claude Code:

```
/plugin install gt-core@graphictreat
/plugin install gt-engineering@graphictreat
/plugin install gt-social@graphictreat
```

Skills are now available in every Claude Code session on your machine, namespaced as `gt-core:<skill-name>`, `gt-engineering:<skill-name>`, and `gt-social:<skill-name>`.

## Verifying the install

Inside Claude Code:

```
/plugin list
```

You should see `gt-core` and `gt-social` listed as installed and enabled.

Try invoking a skill:

```
/gt-core:fan-out-fan-in research the best CDN for image-heavy sites
```

If skills auto-load via description matching, you can also just describe the task naturally and let Claude trigger the right skill.

## Updating

When a teammate pushes a new version of a skill or bumps `version` in `plugin.json`:

```
/plugin update gt-core
```

For safer pinning, point at a git tag instead of `main` by editing your marketplace entry (or in a project's `.claude/settings.json` via `extraKnownMarketplaces`).

## Disabling a single skill

If a particular skill doesn't fit your workflow:

```
/plugin disable gt-social:x-post-gen
```

The plugin stays installed; only that skill is hidden.

## Uninstall

```
/plugin uninstall gt-core@graphictreat
/plugin marketplace remove graphictreat
```

## How to add or modify a skill

1. Clone this repo: `gh repo clone graphictreat/claude-plugins`
2. Add a new directory under `plugins/gt-core/skills/<your-skill>/` with a `SKILL.md` (frontmatter required: `name`, `description`).
3. Test locally by pointing at your checkout: `/plugin marketplace add /absolute/path/to/claude-plugins` then `/plugin install gt-core`.
4. Bump `version` in `plugins/gt-core/.claude-plugin/plugin.json`.
5. Open a PR. After merge, tag a release: `git tag v0.2.0 && git push --tags`.
6. Teammates pull the update with `/plugin update gt-core`.

## Skill scope: when to add a skill here vs in a project repo

**Add to `gt-core` (this repo) when:**
- The skill is useful across two or more projects.
- It's pure-markdown or has no native dependencies (no `npm install`, no Python venv).

**Keep in a project's `.claude/skills/` when:**
- The skill is project-specific (e.g., `webgen`'s `fal-gen`).
- The skill has runtime dependencies that need installing (npm, pip). Plugin install does not run `npm install`; project install does.

For runtime-dep skills you want shared anyway, bundle them into a single self-contained file with esbuild (or equivalent) before promoting them here.

## Adding a second plugin

When `gt-core` grows beyond ~8 skills, split by theme:

1. Create `plugins/gt-web/`, `plugins/gt-mobile/`, etc., each with its own `.claude-plugin/plugin.json` and `skills/`.
2. Add an entry for each in `.claude-plugin/marketplace.json`.
3. Install separately: `/plugin install gt-web@graphictreat`.

## Pinning a project to a specific version

In any project repo, commit a `.claude/settings.json` like:

```json
{
  "extraKnownMarketplaces": {
    "graphictreat": {
      "type": "github",
      "repo": "graphictreat/claude-plugins",
      "ref": "v0.1.0"
    }
  }
}
```

When a teammate trusts the folder, Claude Code prompts them to install this exact version of the marketplace and any plugins it references. Zero-touch onboarding per project.

## Troubleshooting

- **"Cannot read marketplace"** — check `gh auth status`; you likely need `gh auth login` with `repo` scope.
- **Skills don't appear after install** — restart Claude Code; structural changes (new skill dirs) require a session restart.
- **Auto-update silently fails on private repos** — set `GITHUB_TOKEN` in your shell env, or run `/plugin update gt-core` manually.
- **Skill name collides with a personal skill** — plugin skills are namespaced (`gt-core:foo`) and never collide; if you can't invoke yours by short name, the personal one is shadowed by plugin precedence — invoke explicitly with the full name.

## TODO

Tracking work still needed before this marketplace is feature-complete.

### `gt-engineering`

The plugin is registered with `setup-gt-dev-skills` (wired up with its seed templates `issue-tracker-github.md`, `triage-labels.md`, `domain.md`) and `to-product-req` (turns the current conversation into a PRD and publishes it to the issue tracker). (The local-markdown issue tracker option was dropped; GitHub or a user-described tracker only.)

**Missing downstream skills** — `setup-gt-dev-skills` still references downstream skills that don't yet exist in this plugin. Either author them here or remove the references from the setup skill:

- [ ] `triage` — runs an incoming issue through the five-state triage machine (`needs-triage` → `needs-info` / `ready-for-agent` / `ready-for-human` / `wontfix`) using the label vocabulary from `docs/agents/triage-labels.md`.
- [ ] `tdd` — TDD loop that consults domain docs before writing tests.

### Housekeeping

- [x] Tag a release once `gt-engineering` ships (`v0.3.0` for `gt-core` bumping to include `grill-me`, plus the new `gt-engineering` plugin entry).
- [ ] Consider whether `grill-me` belongs under `gt-engineering` (planning/design context) instead of `gt-core` (orchestration) — it's currently in `gt-core` because it's project-agnostic.
