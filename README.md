# rock-agents

English | [简体中文](./README.zh-CN.md)

Specialized AI agent plugin bundle for **ROCK Agent SDK** development and **Harbor / Bash Job** debugging on the ROCK platform (supporting Claude Code, Cursor, Windsurf, Codex, OpenCode, and other skill-compatible tools).

- **ROCK (Reinforcement Open Construction Kit)** is Alibaba's sandbox environment management framework built for agentic reinforcement learning and coding-agent evaluation.
- **Harbor** is the agent benchmark evaluation framework executed inside ROCK sandboxes.

> **Language note**: The skill files (`SKILL.md`), [`docs/installation.md`](./docs/installation.md), and [`docs/plugin-spec.md`](./docs/plugin-spec.md) in this repository are written primarily in Simplified Chinese alongside YAML/Python examples and CLI commands.

## ROCK Skill Ecosystem

Use this table to choose the right `xdlkc` ROCK repository for your workflow:

| Repository | Scope & Included Skills | Packaging & Install Path | Best For |
|---|---|---|---|
| [`rock-marketplace`](https://github.com/xdlkc/rock-marketplace) | Unified `rock` plugin (`v1.4.7`): `rock-cli` (v2.7), `rock-debug`, `rock-agent-debug` (remote + in-sandbox), `rock-eval`, `rock-feedback` | `.claude-plugin/marketplace.json` (`/plugin install rock@rock-marketplace`) & `skills/` symlinks (`npx skills add`) | Most users: full ROCK CLI v2.7, sandbox diagnostics, remote/sandbox job debugging, and batch Harbor regression evaluation |
| **[`rock-agents`](https://github.com/xdlkc/rock-agents)** *(this repo)* | Two plugins: `rock-agent-sdk` (`v1.0.0`: Python `rock.sdk.agent` & `rock_agent_config.yaml`) and `rock-agent` (`v1.3.0`: in-sandbox `rock-agent-debug`) | `.claude-plugin/marketplace.json` (`/plugin install rock-agent-sdk@rock-agents` & `rock-agent@rock-agents`) & `skills/` symlinks (`npx skills add`) | Developers writing Python SDK / YAML benchmark integrations (**`rock-agent-sdk` lives exclusively here**) or installing `rock-agent` standalone |
| [`rock-cli-skills`](https://github.com/xdlkc/rock-cli-skills) | Standalone `rock-cli` skill (`SKILL.md` + `references/sandbox.md`) covering core sandbox lifecycle, `exec`, `upload`/`download`, `attach`, `log`, `history`, `replay` | Root `SKILL.md` (`npx skills add xdlkc/rock-cli-skills` or manual copy; no `.claude-plugin/` wrapper) | Minimal single-skill setup focused solely on `rockcli` sandbox operations |

## Included Plugins & Skills

This repository ships two independent Claude Code plugins (also exposed via top-level `skills/` symlinks for `npx skills add`):

| Plugin | Version | Skill | Focus & Relationship to `rock-marketplace` | Source |
|---|---|---|---|---|
| `rock-agent-sdk` | `1.0.0` | `rock-agent-sdk` | **Unique to `rock-agents`**: Python `rock.sdk.agent` API (`Job`, `JobConfig`, `RockEnvironmentConfig`, `AgentConfig`, `VerifierConfig`, `JobResult`, `TrialResult`), Harbor job YAML templates (SWE-bench Verified, Terminal Bench 2, OSS registries), and `rock_agent_config.yaml` agent integration | [`SKILL.md`](./plugins/rock-agent-sdk/skills/rock-agent-sdk/SKILL.md) |
| `rock-agent` | `1.3.0` | `rock-agent-debug` | In-sandbox Harbor Job & Bash Job debugging via `rockcli sandbox <id> exec`, `result.json`, `trajectory.json`, Docker `hb__*` inspection, batch `scan_job.py`, and Markdown reports *(Note: [`rock-marketplace`](https://github.com/xdlkc/rock-marketplace) carries a newer `rock-agent-debug` adding remote `rc agent view` / `rc agent fs` inspection)* | [`SKILL.md`](./plugins/rock-agent/skills/rock-agent-debug/SKILL.md) |

## Installation

### Option 1: Claude Code Plugin Marketplace

```text
/plugin marketplace add xdlkc/rock-agents
/plugin install rock-agent-sdk@rock-agents
/plugin install rock-agent@rock-agents
```

### Option 2: Vercel Labs `skills` CLI (Multi-Agent)

Works across Claude Code, Cursor, Windsurf, Codex, OpenCode, GitHub Copilot, Cline, Goose, Replit, Kimi CLI, and Qwen Code:

```bash
# List available skills
npx skills add xdlkc/rock-agents --list

# Install both skills globally (or omit -g for project-local .agents/skills/)
npx skills add xdlkc/rock-agents --all -g

# Install only the Python SDK skill
npx skills add xdlkc/rock-agents --skill rock-agent-sdk -g

# Target a specific agent
npx skills add xdlkc/rock-agents -a claude-code -g
```

### Option 3: Manual Clone

```bash
git clone https://github.com/xdlkc/rock-agents.git
cp -r rock-agents/plugins/rock-agent-sdk/skills/rock-agent-sdk ~/.claude/skills/
cp -r rock-agents/plugins/rock-agent/skills/rock-agent-debug ~/.claude/skills/
```

See [`docs/installation.md`](./docs/installation.md) (in Chinese) for the full list of agent paths and uninstall/update commands.

## Repository Structure

```text
rock-agents/
├── .claude-plugin/
│   └── marketplace.json                # Claude Code /plugin marketplace manifest
├── marketplace/
│   └── registry.json                   # Plugin registry metadata
├── plugins/
│   ├── rock-agent/
│   │   ├── plugin.json                 # v1.3.0 metadata
│   │   ├── hooks/hooks.json
│   │   └── skills/rock-agent-debug/SKILL.md
│   └── rock-agent-sdk/
│       ├── plugin.json                 # v1.0.0 metadata
│       ├── hooks/hooks.json
│       └── skills/rock-agent-sdk/SKILL.md
├── skills/                             # Symlinks -> ../plugins/*/skills/* (for npx skills add)
└── docs/
    ├── installation.md                 # Multi-agent installation guide
    └── plugin-spec.md                  # Plugin & SKILL.md format spec
```

## Plugin Authoring

See [`docs/plugin-spec.md`](./docs/plugin-spec.md) for the `plugin.json`, `SKILL.md` frontmatter, and `hooks.json` conventions used in this repository.
