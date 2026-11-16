# rock-agents

[English](./README.md) | 简体中文

聚焦 **ROCK Agent SDK** 开发与沙箱内 **Harbor / Bash Job** 调试的专属 AI Agent 插件包（支持 Claude Code、Cursor、Windsurf、Codex、OpenCode 等工具）。

- **ROCK (Reinforcement Open Construction Kit)**：阿里巴巴开发的沙箱环境管理框架，专为 Agentic 强化学习与 Coding Agent 评测场景设计。
- **Harbor**：运行在 ROCK 沙箱中的 Agent Benchmark 评测框架。

## ROCK Skill 生态对比

`xdlkc` 下共有三个与 ROCK 相关的技能仓库，定位与差异如下：

| 仓库 | 定位与包含技能 | 分发与安装方式 | 适用人群 |
|---|---|---|---|
| [`rock-marketplace`](https://github.com/xdlkc/rock-marketplace) | 统一 `rock` 插件（`v1.4.7`）：`rock-cli`（v2.7）、`rock-debug`、`rock-agent-debug`（支持远程查询 + 沙箱内排查）、`rock-eval`、`rock-feedback` | `.claude-plugin/marketplace.json`（`/plugin install rock@rock-marketplace`）及顶层 `skills/` 软链（`npx skills add`） | 绝大多数用户：需要完整 ROCK CLI v2.7、沙箱排查、Job 调试与 Harbor 全量回归评测 |
| **[`rock-agents`](https://github.com/xdlkc/rock-agents)** *(本仓库)* | 两个独立插件：`rock-agent-sdk`（`v1.0.0`：Python `rock.sdk.agent` 与 `rock_agent_config.yaml`）与 `rock-agent`（`v1.3.0`：沙箱内 `rock-agent-debug`） | `.claude-plugin/marketplace.json`（`/plugin install rock-agent-sdk@rock-agents` 与 `rock-agent@rock-agents`）及顶层 `skills/` 软链（`npx skills add`） | **需要编写 Python SDK / YAML 评测配置的开发者**（`rock-agent-sdk` 仅在此仓库）或需单独安装 `rock-agent` 的场景 |
| [`rock-cli-skills`](https://github.com/xdlkc/rock-cli-skills) | 单技能独立包（`SKILL.md` + `references/sandbox.md`），聚焦 `rockcli` 核心沙箱生命周期、`exec`、上传下载、`attach`、`log`、`history`、`replay` | 根目录 `SKILL.md`（`npx skills add xdlkc/rock-cli-skills` 或手动复制；无 `.claude-plugin/` 包装） | 仅需 `rockcli` 沙箱基础操作、希望最小化单技能安装的用户 |

## 包含插件与技能

本仓库拆分为两个独立的 Claude Code 插件，同时在顶层 `skills/` 提供软链以兼容 `npx skills add`：

| 插件 | 版本 | 包含技能 | 核心内容与生态定位 | 源文件 |
|---|---|---|---|---|
| `rock-agent-sdk` | `1.0.0` | `rock-agent-sdk` | **本仓库独有**：Python `rock.sdk.agent` API（`Job`、`JobConfig`、`RockEnvironmentConfig`、`AgentConfig`、`VerifierConfig`、`JobResult`、`TrialResult`）、SWE-bench Verified / Terminal Bench 2 / OSS 数据集 YAML 模板，以及 `rock_agent_config.yaml` Agent 接入指南 | [`SKILL.md`](./plugins/rock-agent-sdk/skills/rock-agent-sdk/SKILL.md) |
| `rock-agent` | `1.3.0` | `rock-agent-debug` | 基于 `rockcli sandbox <id> exec` 的沙箱内 Harbor Job / Bash Job 排查（`result.json`、`trajectory.json`、`hb__*` Docker 容器排查、批量 `scan_job.py` 与落盘报告）*（注：[`rock-marketplace`](https://github.com/xdlkc/rock-marketplace) 中维护着功能更新的 `rock-agent-debug`，额外支持免存活沙箱的远程 `rc agent view` / `rc agent fs` 路径）* | [`SKILL.md`](./plugins/rock-agent/skills/rock-agent-debug/SKILL.md) |

## 安装

### 方式一：Claude Code Plugin Marketplace

```text
/plugin marketplace add xdlkc/rock-agents
/plugin install rock-agent-sdk@rock-agents
/plugin install rock-agent@rock-agents
```

### 方式二：Skills CLI（跨工具一键安装）

支持 Claude Code、Cursor、Windsurf、Codex、OpenCode、GitHub Copilot、Cline、Goose、Replit、Kimi CLI 与 Qwen Code：

```bash
# 列出可用技能
npx skills add xdlkc/rock-agents --list

# 全局安装全部技能（去掉 -g 则安装到当前项目 .agents/skills/）
npx skills add xdlkc/rock-agents --all -g

# 仅安装 Agent SDK 技能
npx skills add xdlkc/rock-agents --skill rock-agent-sdk -g

# 安装到指定 Agent
npx skills add xdlkc/rock-agents -a claude-code -g
```

### 方式三：手动克隆复制

```bash
git clone https://github.com/xdlkc/rock-agents.git
cp -r rock-agents/plugins/rock-agent-sdk/skills/rock-agent-sdk ~/.claude/skills/
cp -r rock-agents/plugins/rock-agent/skills/rock-agent-debug ~/.claude/skills/
```

完整工具目录对照表与更新/卸载方式见 [`docs/installation.md`](./docs/installation.md)。

## 仓库结构

```text
rock-agents/
├── .claude-plugin/
│   └── marketplace.json                # Claude Code /plugin marketplace 清单
├── marketplace/
│   └── registry.json                   # 插件注册表元数据
├── plugins/
│   ├── rock-agent/
│   │   ├── plugin.json                 # v1.3.0 元数据
│   │   ├── hooks/hooks.json
│   │   └── skills/rock-agent-debug/SKILL.md
│   └── rock-agent-sdk/
│       ├── plugin.json                 # v1.0.0 元数据
│       ├── hooks/hooks.json
│       └── skills/rock-agent-sdk/SKILL.md
├── skills/                             # 顶层软链 -> ../plugins/*/skills/*（兼容 npx skills add）
└── docs/
    ├── installation.md                 # 多 Agent 安装指南
    └── plugin-spec.md                  # 插件与 SKILL.md 格式规范
```

## 插件开发规范

新增或修改插件请参考 [`docs/plugin-spec.md`](./docs/plugin-spec.md) 中的 `plugin.json`、`SKILL.md` frontmatter 与 `hooks.json` 约定。
