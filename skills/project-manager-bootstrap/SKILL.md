---
name: project-manager-bootstrap
description: Bootstrap an Agent in Project Manager Mode for fast prototyping with seven control points (goal lock, scope guard, decision log, progress report, acceptance lock, stop line, review gate). Use when validating ideas, building a runnable demo, end-to-end prototyping, or experimenting with a tech stack on a branch.
---

# project-manager-bootstrap

> 主题分组：execute  
> 来源：`docs/02-像项目主管一样使用 Agent.md` §3「七个控制点」、§4.1 参考 Prompt

## 1. 目的与适用边界

- **目的**：在 Project Manager Mode 下让 Agent **自主推进、快速搭建可运行 demo**，同时保证目标 / 假设 / 决策 / 阶段汇报 / 验收 / 停止线 / 审查门 七个控制点齐全，避免「快速」滑向「失控」。
- **适用**：想法验证、原型、走通端到端闭环、技术栈试探。
- **不适用**：长期项目核心架构、需要逐步训练设计判断的模块（用 [`designer-design-card`](../designer-design-card/SKILL.md)）。

## 2. 输入 / 输出

### 输入（粘贴给 Agent 的内容）

```markdown
你现在进入 Project Manager Mode：项目主管式快速搭建模式。

目标：在一个 session 中尽快搭建一个完整、可运行、可验证的项目 demo。
本次 demo 的端到端闭环：
1. <用户能完成的核心操作>
2. <系统能保存或展示的核心状态>
3. <出错时的基本提示>
4. <我能通过 README 运行和验证它>

工作方式：
1. 你可以自主判断下一步，不需要每步等我确认。
2. 优先完成端到端核心闭环，而不是追求完美架构。
3. 你可以做最小合理假设，但必须记录到 ASSUMPTIONS.md。
4. 所有关键设计选择必须记录到 DECISIONS.md。
5. 不要过度引入复杂框架、抽象或不必要的基础设施。
6. 临时方案用 TODO(demo) 或 FIXME 标记。
7. 每完成一个阶段，输出阶段汇报：
   - 完成了什么 / 修改文件 / 如何运行 / 如何验证 / 当前风险 / 下一阶段
8. 当核心闭环跑通后，停止新增功能。
9. 最终输出：项目结构 / 启动方式 / 功能清单 / 验证方式 / 已知问题 / 临时方案 / 后续重构计划。

不做：
- <复杂权限 / 多租户 / 支付 / 云部署 / 高级缓存 / 微服务拆分 / 插件系统 / 大规模性能优化（按需删改）>

停止线：核心闭环跑通后立刻停止扩张，转入收束。
```

### 输出（期望 Agent 返回的内容）

每阶段一份阶段汇报，最后一份**收束报告**（见 [`stop-and-converge`](../stop-and-converge/SKILL.md)）。

## 3. 执行步骤

1. 通过 [`declare-session-mode`](../declare-session-mode/SKILL.md) 声明 Project Manager Mode。
2. 填好上面 prompt 的 4 项闭环 + 不做清单。
3. 粘贴并启动 Agent 自主推进。
4. 每收到阶段汇报：快速扫一眼**修改文件清单**和**风险**两段；不深入 review 细节。
5. 出现以下任一信号 → 立刻执行 [`stop-and-converge`](../stop-and-converge/SKILL.md)：
   - 项目可本地启动；
   - 核心用户路径走通；
   - Agent 开始建议加「高级功能」；
   - 目录结构开始膨胀；
   - 你已无法在脑中描述主要模块关系。
6. 收束完成 → 切换到 [`review-architecture-audit`](../review-architecture-audit/SKILL.md)。

## 4. 失败处理 / 回滚

- **失败信号 A：过度扩张** —— 实现了 prompt 没要求的高级功能。  
  动作：发送 [`stop-and-converge`](../stop-and-converge/SKILL.md) 中「停止扩张 prompt」，要求列出超范围功能并撤回或推迟。
- **失败信号 B：阶段汇报缺失** —— Agent 一路写到底没汇报。  
  动作：发送「停止。请按阶段汇报格式列出已做工作和当前文件清单。」
- **失败信号 C：决策无记录** —— 引入了新依赖 / 新框架但 DECISIONS.md 没更新。  
  动作：要求 Agent 把所有自主选择补录到 `ASSUMPTIONS.md` / `DECISIONS.md`，参考 [`decision-assumption-log`](../decision-assumption-log/SKILL.md)。

## 5. 示例

### 示例 1：CLI 任务管理 demo

**输入**（节选 4 项闭环）：

```markdown
1. 用户能 create / list / done 一个任务
2. 系统把任务持久化到本地 JSON
3. 找不到任务时给出明确报错
4. README 写明 `python -m app create "..."` 等命令，能复现
```

**Agent 阶段汇报示例**：

```markdown
# 阶段进展（阶段 2/3）
完成了什么：CLI 入口 + JSON 存储 + create/list 命令
涉及文件：app/__main__.py, app/store.py, README.md
如何运行：python -m app create "buy milk"
如何验证：再跑 python -m app list 应看到该条目
当前假设：单用户、单文件、不并发（已记入 ASSUMPTIONS.md）
当前风险：JSON 文件无锁，并发写会丢
下一阶段：实现 done + 错误提示，然后收束
```

## 6. 关联 skill

- 上游：[`declare-session-mode`](../declare-session-mode/SKILL.md)
- 配套：[`decision-assumption-log`](../decision-assumption-log/SKILL.md)
- 收束：[`stop-and-converge`](../stop-and-converge/SKILL.md)
- 下游：[`review-architecture-audit`](../review-architecture-audit/SKILL.md)
