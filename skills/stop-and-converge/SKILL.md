---
name: stop-and-converge
description: Pull a session back from "keep producing" to "finish delivering" when the Agent expands beyond scope, the core loop already runs, or a single diff is too large to review. Use when the Agent suggests "let's also add X", a turn touches more than 3 files without explanation, or you can no longer describe the project structure in your head.
---

# stop-and-converge

> 主题分组：execute  
> 来源：`docs/01-像设计师一样使用 Agent.md` §6.3/6.4 打断与收束 prompt；`docs/02-像项目主管一样使用 Agent.md` §3.5/§3.6 验收锁与停止线、§4.3/4.4 收束 prompt

## 1. 目的与适用边界

- **目的**：当 Agent 出现**过度扩张**或**改动过大无法审查**时，立即把 session 从「继续产出」拉回「完成交付」，并产出一份可接管的收束报告。
- **适用**：Project Manager Mode 后期；Designer Mode 单轮 diff 超界；任何「我已经审不动」的时刻。
- **不适用**：刚开始的探索期（应该让 Agent 先把闭环跑通）。

## 2. 输入 / 输出

### 输入（粘贴给 Agent 的内容）

#### A. 打断扩张（轻量，回到当前 checkpoint）

```markdown
停止扩展。

回到当前 checkpoint。
列出已实现功能，标记哪些超出了本轮目标。
给出可以删除或推迟的部分。
回到核心闭环，优先保证它可运行、可验证。
```

#### B. 收束（重型，结束本 session）

```markdown
核心闭环已经跑通。现在停止新增功能，进入收束阶段。

请输出：
1. 项目结构说明
2. 启动方式
3. 核心用户路径
4. 已实现功能
5. 未实现功能
6. 已知问题
7. 临时方案（TODO(demo) / FIXME 清单）
8. 后续重构建议
9. 最值得人工审查的 5 个文件

不要继续写新代码。
```

#### C. 改动过大无法审查时（拆分）

```markdown
这次改动太大，我无法审核。

请不要继续写新代码。
把当前改动拆成 3 个 commit 级别的步骤：
1. 基础结构
2. 核心逻辑
3. 验证与测试

每一步说明：涉及文件 / 设计理由 / 验证方式。
```

### 输出（期望 Agent 返回的内容）

按 prompt A/B/C 对应给出**清单 / 收束报告 / 拆分计划**，且**不再生成新代码**。

## 3. 执行步骤

1. 识别触发信号（见下文）。
2. 选择 A / B / C 之一发送：
   - 仅扩张但还没结束 → A
   - 闭环跑通 / session 该结束 → B
   - 单轮改动过大 → C
3. 收到产物后，**只读不改**，确认「最值得审查的 5 个文件」可被打开。
4. 收束完成后，git 提交一个 `chore: converge demo` 标签节点，便于回滚。
5. 切换到下一个 skill：[`review-architecture-audit`](../review-architecture-audit/SKILL.md)。

### 触发信号速查

| 信号 | 选哪个 |
| --- | --- |
| Agent 在加 prompt 没要求的高级功能 | A |
| 项目能跑了 / 我已经描述不出模块关系 | B |
| 一次 diff 改了 8+ 文件 / 引入新框架 | C |

## 4. 失败处理 / 回滚

- **Agent 仍继续写代码** → 发送「停止。本轮禁止新增任何代码。仅输出 <prompt 名> 要求的清单。」
- **Agent 输出空泛**（例如「项目跑通了，建议继续优化」） → 要求逐项给出**文件路径 + 行号 / 函数名**，不接受不可定位的描述。
- **回滚到收束前**：使用 git 最后一个稳定提交回退；本 skill 强烈建议在调用前先 `git commit` 一次。

## 5. 示例

### 示例 1：demo 阶段中段过度扩张

**症状**：你只要求 create / list / done，Agent 顺便加了登录、SQLite、Web UI。

**输入**：使用 prompt A。

**Agent 应输出**：

```markdown
已实现功能：
- create / list / done（在范围内）
- 用户登录（超范围）
- SQLite 持久化（超范围）
- Web UI（超范围）

可以删除：Web UI（无依赖）、登录（仅 1 个中间件）
推迟：SQLite → 回退到 JSON，记入 DECISIONS.md「demo 阶段不引入数据库」

回到核心闭环：保留 app/cli.py + app/store_json.py + README，闭环可运行。
```

## 6. 关联 skill

- 上游：[`project-manager-bootstrap`](../project-manager-bootstrap/SKILL.md) / [`designer-design-card`](../designer-design-card/SKILL.md)
- 下游：[`review-architecture-audit`](../review-architecture-audit/SKILL.md)
- 配套日志：[`decision-assumption-log`](../decision-assumption-log/SKILL.md)
