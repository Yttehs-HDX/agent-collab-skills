# agent-collab-skills

> 来源：本仓库的全部 skills 抽取并整理自 [`docs/`](docs/) 下的三篇笔记：[01-像设计师一样使用 Agent](docs/01-像设计师一样使用%20Agent.md)、[02-像项目主管一样使用 Agent](docs/02-像项目主管一样使用%20Agent.md)、[03-两种 Agent 使用模式的协作与切换](docs/03-两种%20Agent%20使用模式的协作与切换.md)。每个 skill 都对应原笔记中可被复用的协作流程或工作机制。

一组**多 Agent 协作可复用技能（skills）**，专注于人类开发者与 Coding Agent 之间的**协作协议**：何时放权、何时小步、何时收束、何时审查、何时加固。

## 定位

- 不是 prompt 大全，而是有边界、可执行、可回滚的**协作 skill**。
- 每个 skill 是一个**可复制粘贴到 session 开头**的工作卡片：明确目的、输入、步骤、失败处理、示例。
- 适合配合任意 Coding Agent（Copilot、Claude Code、Cursor、Codex 等）使用。
- 通过 `npx skills add <skill>` 即装即用，符合 [skills.sh](https://skills.sh/) registry 规范。
- 也支持**自动路由**：把 [AGENTS.md](AGENTS.md) 作为 system prompt / `AGENTS.md` 加载，Agent 会按用户意图自动选择对应 skill，无需手动粘贴卡片。

## 适用场景

- 同一个项目里需要在「快速 demo」和「长期可维护」之间切换。
- Agent 一次性产出过多，自己审不动 / 接管不了。
- 想把模糊的「让 Agent 写代码」升级为**有协议、可追踪、可收束**的协作。

## 快速开始

通过 [skills CLI](https://skills.sh/) 安装，需要 Node.js ≥ 18。

### 一次性安装全部 skills（推荐）

```bash
npx skills add Yttehs-HDX/agent-collab-skills@declare-session-mode \
               Yttehs-HDX/agent-collab-skills@designer-design-card \
               Yttehs-HDX/agent-collab-skills@project-manager-bootstrap \
               Yttehs-HDX/agent-collab-skills@stop-and-converge \
               Yttehs-HDX/agent-collab-skills@review-architecture-audit \
               Yttehs-HDX/agent-collab-skills@decision-assumption-log \
               Yttehs-HDX/agent-collab-skills@mode-switch-gate
```

加 `-g -y` 可全局静默安装（跨项目可用）：

```bash
npx skills add Yttehs-HDX/agent-collab-skills@declare-session-mode \
               Yttehs-HDX/agent-collab-skills@designer-design-card \
               Yttehs-HDX/agent-collab-skills@project-manager-bootstrap \
               Yttehs-HDX/agent-collab-skills@stop-and-converge \
               Yttehs-HDX/agent-collab-skills@review-architecture-audit \
               Yttehs-HDX/agent-collab-skills@decision-assumption-log \
               Yttehs-HDX/agent-collab-skills@mode-switch-gate -g -y
```

安装后，`SKILL.md` 会被复制到项目的 `.skills/` 或 `~/.agents/skills/` 下，Coding Agent 会自动读取。

### 按需安装单个 skill

```bash
npx skills add Yttehs-HDX/agent-collab-skills@<skill-name>
```

可选 skill 名称见 [skills/index.md](skills/index.md) 或下方使用指南。

### 搜索本仓库

```bash
npx skills find agent-collab
```

## 使用指南

安装完成后，按项目阶段选择对应 skill：

### 1. 每个 session 开头：声明模式

```bash
npx skills add Yttehs-HDX/agent-collab-skills@declare-session-mode
```

**何时用**：打开任何 Agent 对话前，先声明当前是 Designer / Project Manager / Review / Harden 哪种模式。这是避免 Agent 与你预期错位的最小成本动作。

> 快速原则：**模式未声明是最危险的状态。**

---

### 2. 快速搭原型（Project Manager Mode）

```bash
npx skills add Yttehs-HDX/agent-collab-skills@project-manager-bootstrap
npx skills add Yttehs-HDX/agent-collab-skills@decision-assumption-log
```

**何时用**：想在一个 session 内让 Agent 自主推进，快速跑通端到端 demo。  
**配套**：`decision-assumption-log` 强制 Agent 把假设和技术选择记录到 `ASSUMPTIONS.md` / `DECISIONS.md`，防止产出变黑箱。

---

### 3. 原型跑通后：收束与审查

```bash
npx skills add Yttehs-HDX/agent-collab-skills@stop-and-converge
npx skills add Yttehs-HDX/agent-collab-skills@review-architecture-audit
```

**何时用**：核心路径已经可以运行、或 Agent 开始建议「顺便加 X」时。  
先用 `stop-and-converge` 让 Agent 停止产出，再用 `review-architecture-audit` 输出 10 段架构审查报告，区分真实核心与 demo 临时方案。

---

### 4. 重构核心逻辑（Designer Mode）

```bash
npx skills add Yttehs-HDX/agent-collab-skills@designer-design-card
```

**何时用**：需要改动核心抽象、模块边界、状态模型，或任何「改错了很难回滚」的地方。  
Skill 会要求 Agent 每轮先发设计卡片，经你确认后才能写代码，每轮最多改 3 个文件。

---

### 5. 在里程碑节点自检：决定下一步模式

```bash
npx skills add Yttehs-HDX/agent-collab-skills@mode-switch-gate
```

**何时用**：每次阶段汇报之后，或「感觉不对但说不出来」的时候。  
Skill 提供一张自检卡片和信号表，帮你判断应该继续当前模式、切到 Review、切到 Designer，还是先 `stop-and-converge`。

---

### 推荐生命周期

```text
新想法
  └─ declare-session-mode（声明 PM Mode）
       └─ project-manager-bootstrap ───────────────────┐
          + decision-assumption-log（全程记录）          │
                                                       ▼
                                            stop-and-converge（闭环跑通后）
                                                       │
                                                       ▼
                                            review-architecture-audit
                                                       │
                                                       ▼
                                        declare-session-mode（声明 Designer Mode）
                                                       │
                                                       ▼
                                           designer-design-card（逐片重构）
                                                       │
                                                  mode-switch-gate
                                               （每里程碑自检，决定继续/切换）
```

详细各 skill 说明见 [skills/index.md](skills/index.md)。

## 目录索引

- [AGENTS.md](AGENTS.md) — 自动路由层：把 skills 编排成 system prompt / `AGENTS.md`，让 Agent 无感调用
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

MIT — 见 [LICENSE](LICENSE)。
