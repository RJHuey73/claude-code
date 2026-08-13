# CLAUDE.md

Guidance for Claude Code (claude.ai/code) when working in this repository.

## What This Is

This is `RJHuey73/claude-code`, a fork of the **public `claude-code` repository**
(upstream: `anthropics/claude-code`). Claude Code itself — the CLI/agent binary —
is **closed-source and distributed separately** (via `curl | bash`, Homebrew,
WinGet, or the deprecated `npm install -g @anthropic-ai/claude-code`). This repo
does **not** contain that source. There is no `package.json`, no build, no lint,
no test suite, and no application code to compile or run here.

What this repo *does* contain:

- **Official plugins** (`plugins/`) — example Claude Code plugins (slash
  commands, agents, skills, hooks) that ship as reference implementations of
  the plugin system.
- **GitHub issue-automation scripts** (`scripts/`) — TypeScript (run via
  `bun`) and shell scripts that power this repo's own issue triage/lifecycle
  bots (duplicate detection, stale-issue closing, label management).
- **Example configs** (`examples/`) — sample `hooks`, MDM (mobile device
  management) policy templates, and `settings.json` permission profiles.
- **GitHub Actions workflows** (`.github/workflows/`) — including
  `claude.yml`, which runs Claude Code itself against issues/PRs when
  `@claude` is mentioned, plus the issue-lifecycle/dedupe automation these
  scripts implement.
- Top-level docs: `README.md`, `CHANGELOG.md` (the real, fine-grained release
  notes for the CLI), `SECURITY.md`, `LICENSE.md`.

## Layout

| Path | Purpose |
|------|---------|
| `plugins/` | Official example plugins; see `plugins/README.md` for the full table of what each one provides (commands/agents/skills/hooks) and the standard plugin structure (`.claude-plugin/plugin.json`, `commands/`, `agents/`, `skills/`, `hooks/`, `.mcp.json`). |
| `.claude-plugin/marketplace.json` | Marketplace manifest listing the plugins in `plugins/` for `/plugin` installation. |
| `scripts/` | Bun/TypeScript + shell scripts backing this repo's issue bots: `issue-lifecycle.ts`, `sweep.ts`, `auto-close-duplicates.ts`, `backfill-duplicate-comments.ts`, `comment-on-duplicates.sh`, `edit-issue-labels.sh`, `gh.sh` (a `gh` wrapper). |
| `examples/hooks/` | Sample hook script (`bash_command_validator_example.py`) demonstrating the hooks API. |
| `examples/settings/` | Sample `settings.json` permission profiles (`settings-strict.json`, `settings-lax.json`, `settings-bash-sandbox.json`) with a `README.md` explaining trade-offs. |
| `examples/mdm/` | Enterprise MDM policy templates for macOS (`.plist`/`.mobileconfig`) and Windows (`.admx`, PowerShell). |
| `.github/workflows/` | `claude.yml` (the `@claude` mention responder), `claude-issue-triage.yml`, `claude-dedupe-issues.yml`, plus lifecycle/dedupe/lock automation that call into `scripts/`. |
| `.devcontainer/` | Dockerfile + devcontainer config + firewall init script for a sandboxed dev container. |
| `Script/run_devcontainer_claude_code.ps1` | PowerShell helper to launch the devcontainer on Windows. |

## Working in this repo

- **There is nothing to build, lint, or test at the repo root.** Don't invent
  `npm run build`/`npm test` commands — they don't exist here. If you're
  asked to add project-wide tooling, confirm first; it would be new, not
  restoring something missing.
- **Plugin changes**: follow the structure documented in `plugins/README.md`
  exactly (`.claude-plugin/plugin.json` for metadata, comprehensive
  `README.md` per plugin, contents under `commands/`/`agents/`/`skills/`/
  `hooks/`). New plugins should be added to `.claude-plugin/marketplace.json`
  and to the table in `plugins/README.md`.
- **Script changes** (`scripts/*.ts`): these run via `bun` (see the
  `#!/usr/bin/env bun` shebangs), authenticate to GitHub via `GITHUB_TOKEN`,
  and are invoked from the workflows in `.github/workflows/`. Support a
  `--dry-run` flag where present (see `sweep.ts`) — prefer exercising that
  before assuming a change to close/label logic is safe, since these scripts
  act on real, live GitHub issues in this repo.
- **This is a fork.** Upstream is `anthropics/claude-code`. Keep fork-specific
  changes isolated and be mindful that `README.md`/`CHANGELOG.md` describe the
  upstream product, not anything unique to this fork.

## Gotchas

- Don't confuse this repo with the actual Claude Code source tree — questions
  about CLI internals, the Agent SDK implementation, or runtime behavior
  can't be answered by reading code here; at most this repo's `CHANGELOG.md`
  documents *what* shipped, not *how*.
- `examples/` and `plugins/` files are reference material consumed by end
  users of Claude Code (copied into their own projects or installed via
  `/plugin`), not code that this repo itself executes at build/test time.
