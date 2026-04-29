---
name: designer-design-card
description: Force the Agent to produce a design card before writing any code, using the four locks (Scope / Design / Diff / Checkpoint). Use when working on long-lived projects, core abstractions, module boundaries, state models, or public interfaces where you need to review the design rather than the code after the fact.
---
# designer-design-card

> 主题分组：design  
> 来源：`docs/01-像设计师一样使用 Agent.md` §3「四把锁」、§5「设计卡片」、§6.1/6.2 参考 Prompt

## 1. 目的与适用边界

- **目的**：在 Designer Mode 下，强制 Agent **先输出设计卡片，再写代码**；通过 Scope / Design / Diff / Checkpoint 四把锁把改动控制在可审核范围。
- **适用**：长期项目、核心抽象、需要训练自己工程判断的场景。
- **不适用**：探索性 demo、一次性脚本、纯重命名等机械改动。

## 2. 输入 / 输出

### 输入（粘贴给 Agent 的内容）

```markdown
你是我的 co-design implementer，不是 autonomous coder。

工作规则（四把锁）：
1. Scope Lock：本轮只完成一个最小可验证 checkpoint。
2. Design Lock：写代码前必须先给设计卡片，等我确认。
3. Diff Lock：每轮最多修改 3 个文件，超出必须先说明原因。
4. Checkpoint Lock：完成后必须可运行、可验证、可停下。

请先用以下设计卡片格式回答，不要写代码：

# 本轮目标
# 用户路径
# 核心对象
# 状态变化
# 文件影响（清单 + 每个文件做什么）
# 不做什么
# 风险
# 验证方式（运行命令 / 测试 / 手动步骤）
# 等待我确认的问题
```

### 输出（期望 Agent 返回的内容）

一份按上面 9 段填好的设计卡片，**没有代码**。

确认后再发送进入实现的 prompt：

```markdown
可以进入实现。
只实现刚才确认的 checkpoint，不要扩展。
完成后只汇报：修改文件 / 核心 diff 摘要 / 运行方式 / 验证方式 / 下一步建议。
```

## 3. 执行步骤

1. 在 session 开头通过 [`declare-session-mode`](../declare-session-mode/SKILL.md) 声明 Designer Mode。
2. 把上方设计卡片 prompt 粘贴给 Agent。
3. 审阅卡片：重点看 **不做什么 / 文件影响 / 验证方式** 三段是否合理。
4. 如有偏差，**修改卡片再让 Agent 重写**，不要急着进入实现。
5. 卡片 OK 后发送实现 prompt。
6. Agent 完成后，做 Reflection：这个边界合理吗？抽象会不会太早？
7. 进入下一个薄片，回到步骤 2。

## 4. 失败处理 / 回滚

- **失败信号 A**：Agent 跳过卡片直接写代码 → 发送「停止扩展。回到设计卡片。撤回未确认改动。」
- **失败信号 B**：单轮 diff 超过 3 个文件 → 使用 [`stop-and-converge`](../stop-and-converge/SKILL.md) 把改动拆成 3 个独立 commit 级别步骤。
- **失败信号 C**：实现引入了卡片中「不做什么」的内容 → 要求 Agent 列出超范围文件，回退或挪到下一片。

## 5. 示例

### 示例 1：把存储层从业务逻辑里拆出来

**输入**：见上「输入」段。

**Agent 输出（节选）**：

```markdown
# 本轮目标
将 Task 的持久化从 TaskService 中抽到独立的 TaskRepository 接口。

# 不做什么
- 不切换存储后端（仍用现有 SQLite）
- 不改 Task 的字段
- 不动 CLI 入口

# 文件影响
- src/repo/task_repo.py（新建，定义接口 + SQLite 实现）
- src/service/task_service.py（改为依赖接口）
- tests/test_task_service.py（用 fake repo 替换）

# 验证方式
pytest tests/test_task_service.py -k task
python -m app create "demo" && python -m app list

# 等待我确认的问题
- TaskRepository 是否需要 async？当前同步够用，建议先同步。
```

## 6. 关联 skill

- 上游：[`declare-session-mode`](../declare-session-mode/SKILL.md)
- 配套（Agent 失控时）：[`stop-and-converge`](../stop-and-converge/SKILL.md)
- 下游（每片完成后）：仍是本 skill 的下一轮，或切到 [`review-architecture-audit`](../review-architecture-audit/SKILL.md)
