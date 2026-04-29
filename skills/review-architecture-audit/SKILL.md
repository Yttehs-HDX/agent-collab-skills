---
name: review-architecture-audit
description: Insert a Review Mode buffer between fast prototyping and Designer Mode by forcing the Agent to stop writing code and produce a 10-section architecture audit report. Use after a prototype's core loop runs, before any refactor begins, or at every milestone end to separate real core from demo scaffolding, over-engineering, and unclear boundaries.
---

# review-architecture-audit

> 主题分组：review  
> 来源：`docs/02-像项目主管一样使用 Agent.md` §3.7 「Review Gate」、§4.5；`docs/03-两种 Agent 使用模式的协作与切换.md` §2.3「Review Mode 是切换的关口」

## 1. 目的与适用边界

- **目的**：在快速模式与设计师模式之间插入**审查缓冲层**。让 Agent 停止写代码、解释当前系统，区分「真实核心 / demo 临时方案 / 过度设计 / 边界不清」，为下一阶段决定是重构、加固还是丢弃原型。
- **适用**：Project Manager Mode 收束后；准备切到 Designer Mode 之前；每个里程碑结束。
- **不适用**：项目还没有可运行物；正在写新功能（请先 `stop-and-converge`）。

## 2. 输入 / 输出

### 输入：审查 prompt

```markdown
现在进入 Review Mode。

请基于当前项目输出一份架构审查报告：

1. 当前项目的核心闭环是什么？
2. 当前项目最重要的模块有哪些？
3. 哪些设计可以保留？
4. 哪些设计只是 demo 临时方案？
5. 哪些地方存在过度设计？
6. 哪些地方边界不清？
7. 哪些依赖可以移除或替换？
8. 哪些文件最需要我人工审查？
9. 当前最危险的技术债是什么？
10. 如果要转为长期项目，请给出 5 个最小重构 checkpoint。

注意：不要直接改代码。先帮我重新理解项目。
```

### 输出：架构审查报告（10 段）

Agent 必须按 1–10 编号回答；每段引用具体文件 / 行号 / 函数名，不接受抽象描述。

## 3. 执行步骤

1. 确认前置条件：核心闭环已跑通；已经走过 `stop-and-converge`；`ASSUMPTIONS.md` / `DECISIONS.md` 已存在。
2. 粘贴 prompt，等 Agent 输出 10 段报告。
3. 用三色标注每个模块：
   - 🟢 保留（变成长期架构的种子）
   - 🟡 重画（进入 Designer Mode 重构）
   - 🔴 丢弃（demo 临时方案，新阶段直接删）
4. 把第 10 项「5 个最小重构 checkpoint」拆成 5 个 Designer Mode 薄片任务，存成清单。
5. 关闭本 session；下一 session 重新声明 mode = Designer。

## 4. 失败处理 / 回滚

- **Agent 在 Review 中又开始写代码** → 「停。Review Mode 不写代码，只输出文本。」
- **报告太抽象** → 要求每条都引用文件路径和行号；不允许出现「整体架构良好」之类无锚点描述。
- **Agent 把所有模块都说成「保留」** → 让它强制至少标 2 项「demo 临时」+ 2 项「过度设计」，目的是逼出取舍。
- **回滚**：报告本身不会改代码，但若误信报告导致后续重构失败，回退到 Review 之前的 commit。

## 5. 示例

### 示例：task tracker demo 的审查报告（节选）

**输出**：

```markdown
1. 核心闭环：CLI add → store.append → store.read → CLI list
2. 重要模块：src/cli.ts、src/store.ts、src/model.ts
3. 可保留：model.ts 的 Task 数据形状；store 的 read/append 接口形状
4. 临时方案：store.ts 全量 JSON 读写（src/store.ts:14）；CLI 直接 console.log（src/cli.ts:30）
5. 过度设计：src/plugins/（空目录但已注册插件加载逻辑） — 当前无插件需求
6. 边界不清：cli.ts 直接调用 fs（绕过 store 层）src/cli.ts:55
7. 可移除依赖：commander（自写 argv 解析够用了）
8. 最需审查：store.ts、cli.ts、plugins/loader.ts
9. 最危险技术债：并发写 JSON 文件可能损坏数据
10. 5 个重构 checkpoint：
    1) 把 fs 调用集中到 store 层
    2) 删除 plugins/ 整个目录
    3) Task 抽出 Repository 接口
    4) CLI 错误用 Result，统一退出码
    5) 给 store.append 加文件锁或换 SQLite
```

## 6. 关联 skill

- 上游 ↖：[`stop-and-converge`](../stop-and-converge/SKILL.md)
- 下游 ↘：[`designer-design-card`](../designer-design-card/SKILL.md)（按第 10 项的 5 个 checkpoint 切薄片）
- 配套 ：[`mode-switch-gate`](../mode-switch-gate/SKILL.md)（判断该不该切到 Designer / Harden）
