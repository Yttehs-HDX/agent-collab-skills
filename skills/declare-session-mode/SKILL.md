---
name: declare-session-mode
description: Declare the Agent collaboration mode (Project Manager / Designer / Review / Harden) explicitly at the start of every session. Use when starting a new Agent coding session, switching project phases, or whenever the Agent's autonomy level needs to be made explicit to avoid mismatched expectations between developer and Agent.
---

# declare-session-mode

> 主题分组：declare  
> 来源：`docs/03-两种 Agent 使用模式的协作与切换.md` §3「不要混用模式」

## 1. 目的与适用边界

- **目的**：在每次 session 开始前，**显式声明**当前 Agent 使用模式，避免「我以为在共同设计 / Agent 以为被授权自主推进」的预期错位。
- **适用**：任何与 Coding Agent 的新 session、模式切换点、长 session 重新校准时。
- **不适用**：单条问答式查询（不会产生代码改动）。

## 2. 输入 / 输出

### 输入（粘贴给 Agent 的内容）

```markdown
本 session 使用 <Project Manager Mode | Designer Mode | Review Mode | Harden Mode>。

目标：<一句话目标>
授权范围：<Agent 是否可以自主推进；是否需要每步等待我确认>
不做：<明确不做的事项 3-7 条>
完成判据：<本 session 何时算结束>
停止线：<出现什么信号必须停止扩张>
```

### 输出（期望 Agent 返回的内容）

```markdown
我已确认进入 <Mode>。
我对目标 / 授权 / 不做 / 完成判据 / 停止线的理解如下：
- 目标：...
- 授权：我可以 / 不可以 ...
- 不做：...
- 完成判据：...
- 停止线：...

如有歧义的点：
- ...

是否可以开始？
```

## 3. 执行步骤

1. 选定模式（参考 README 表格或 `mode-switch-gate`）。
2. 填空上面 5 个字段。
3. 粘贴给 Agent，**等待它复述确认**。
4. Agent 复述无误后，再进入对应模式的具体 skill（如 `designer-design-card` 或 `project-manager-bootstrap`）。
5. session 中如出现模式漂移，立刻使用本 skill 重新声明。

## 4. 失败处理 / 回滚

- **失败信号**：Agent 跳过复述直接开始写代码 / 复述与你的意图不符 / 中途行为与声明模式矛盾（如 Designer 模式下一次改了 10 个文件）。
- **回滚动作**：
  1. 立刻发送「停止。请回到模式声明。」
  2. 让 Agent 列出已做改动，标记哪些超出当前模式授权。
  3. 重新执行本 skill。

## 5. 示例

### 示例 1：进入 Designer Mode 重构核心模块

**输入**：

```markdown
本 session 使用 Designer Mode。

目标：把 task 状态机从业务逻辑里拆出来，形成独立模块。
授权：写代码前必须先给设计卡片；每轮最多改 3 个文件。
不做：不重构存储层；不改 CLI；不动测试以外的依赖。
完成判据：状态机有独立文件 + 单元测试通过 + 旧调用点全部迁移。
停止线：任何一轮 diff 超过 3 个文件，必须停下来等我确认。
```

**输出**：见上「输出」段，Agent 应复述并提问歧义点。

## 6. 关联 skill

- 下游（Designer Mode）：[`designer-design-card`](../designer-design-card/SKILL.md)
- 下游（Project Manager Mode）：[`project-manager-bootstrap`](../project-manager-bootstrap/SKILL.md)
- 下游（Review Mode）：[`review-architecture-audit`](../review-architecture-audit/SKILL.md)
- 切换时机：[`mode-switch-gate`](../mode-switch-gate/SKILL.md)
