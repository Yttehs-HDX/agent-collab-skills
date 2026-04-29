# agent-collab-skills

> 来源：本仓库的全部 skills 抽取并整理自 [`docs/`](docs/) 下的三篇笔记：[01-像设计师一样使用 Agent](docs/01-像设计师一样使用%20Agent.md)、[02-像项目主管一样使用 Agent](docs/02-像项目主管一样使用%20Agent.md)、[03-两种 Agent 使用模式的协作与切换](docs/03-两种%20Agent%20使用模式的协作与切换.md)。每个 skill 都对应原笔记中可被复用的协作流程或工作机制。

一组**多 Agent 协作可复用技能（skills）**，专注于人类开发者与 Coding Agent 之间的**协作协议**：何时放权、何时小步、何时收束、何时审查、何时加固。

## 定位

- 不是 prompt 大全，而是有边界、可执行、可回滚的**协作 skill**。
- 每个 skill 是一个**可复制粘贴到 session 开头**的工作卡片：明确目的、输入、步骤、失败处理、示例。
- 适合配合任意 Coding Agent（Copilot、Claude Code、Cursor、Codex 等）使用。
- 通过 `npx skills add <skill>` 即装即用，符合 [skills.sh](https://skills.sh/) registry 规范。

## 适用场景

- 同一个项目里需要在「快速 demo」和「长期可维护」之间切换。
- Agent 一次性产出过多，自己审不动 / 接管不了。
- 想把模糊的「让 Agent 写代码」升级为**有协议、可追踪、可收束**的协作。

## 快速开始

通过 [skills CLI](https://skills.sh/) 一键安装到你的项目：

```bash
# 安装单个 skill（推荐）
npx skills add Yttehs-HDX/agent-collab-skills@declare-session-mode

# 全局安装（用户级，跨项目可用）
npx skills add Yttehs-HDX/agent-collab-skills@designer-design-card -g -y

# 浏览本仓库下所有可安装的 skills
npx skills find agent-collab
```

安装后，skill 的 `SKILL.md` 会被复制到你项目的 `.skills/` 或 `~/.agents/skills/` 下，Coding Agent 会自动读取。

或手动使用：

1. 浏览 [skills/index.md](skills/index.md) 找到当前阶段需要的 skill。
2. 打开对应 `skills/<name>/SKILL.md`，把「输入 / Prompt」段直接粘到 Agent session 开头。
3. 项目演化时，参照 [skills/mode-switch-gate/SKILL.md](skills/mode-switch-gate/SKILL.md) 切换 skill。

## 目录索引

- [docs/](docs/) — 原始笔记（skills 的来源依据）
- [skills/](skills/) — 所有 skills，按主题分组见 [skills/index.md](skills/index.md)
- [templates/SKILL_TEMPLATE.md](templates/SKILL_TEMPLATE.md) — 新建 skill 的模板
- [CONTRIBUTING.md](CONTRIBUTING.md) — 贡献规范
- [LICENSE](LICENSE) — MIT（可按需替换）

## 核心理念（来自原笔记）

| 模式 | 关键词 | 何时用 |
| --- | --- | --- |
| Project Manager Mode | 快速、完整、可运行、可收束 | 想法验证、demo、原型 |
| Designer Mode | 边界、薄片、确认、可审核 | 长期项目、核心抽象 |
| Review Mode | 停止新增、解释系统、画地图 | 两种模式之间的缓冲 |
| Harden Mode | 测试、日志、文档、错误处理 | 把 demo 变成可演化工程 |

> 真正危险的不是模式选错，而是**模式未声明**。详见 [skills/declare-session-mode/SKILL.md](skills/declare-session-mode/SKILL.md)。

## License

MIT — 见 [LICENSE](LICENSE)。如需更换（Apache-2.0 / CC-BY 等），直接替换该文件。