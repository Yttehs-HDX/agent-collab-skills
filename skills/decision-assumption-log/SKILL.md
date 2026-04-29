---
name: decision-assumption-log
description: Force the Agent to record assumptions in ASSUMPTIONS.md and key technical decisions (with options, choice, reason, risk) in DECISIONS.md as a lightweight ADR while it works autonomously. Use throughout Project Manager Mode and any time the Agent makes a non-trivial technical choice without an explicit design card.
---

# decision-assumption-log

> 主题分组：log  
> 来源：`docs/02-像项目主管一样使用 Agent.md` §3.3「决策日志」、§4.2

## 1. 目的与适用边界

- **目的**：在 Agent 拥有自主权时，强制把**假设**和**关键决策**落到仓库内的两个文件：`ASSUMPTIONS.md`、`DECISIONS.md`。让快速生成的项目仍然可解释、可审查、可重构。
- **适用**：Project Manager Mode 全程；Designer Mode 中遇到「设计卡片之外的小决策」时。
- **不适用**：纯 UI 微调、命名调整等无架构影响的小改动。

## 2. 输入 / 输出

### 输入 A：开 session 时的规则 prompt

```markdown
本 session 中，你必须按以下规则维护两个日志文件：

ASSUMPTIONS.md —— 当需求不明确时，记录你做了哪些假设。
DECISIONS.md   —— 关键技术 / 架构选择，记录方案、替代方案、理由、风险。

规则：
1. 影响不大的细节：做最小合理假设 → 写入 ASSUMPTIONS.md，继续推进。
2. 影响架构边界的选择：暂停问我，不要自行决定。
3. 涉及安全 / 权限 / 数据删除 / 付费 / 外部服务的事项：必须问，不允许自行决定。
4. 临时方案在代码中标 TODO(demo) 或 FIXME。
5. 每次阶段汇报最后，附「本阶段新增 ASSUMPTIONS / DECISIONS 条数」。
```

### 输入 B：日志文件骨架

`ASSUMPTIONS.md`：

```markdown
# Assumptions

> 当原始需求不明确时所做的最小合理假设。每条都可被未来推翻。

## A-001 <一句话>
- date: YYYY-MM-DD
- where: <相关文件 / 模块>
- why: <为什么这样假设>
- impact: <如果错了会影响什么>
- revisit: <什么条件下需重新评估>
```

`DECISIONS.md`（轻量 ADR）：

```markdown
# Decisions

## D-001 <决定一句话>
- date: YYYY-MM-DD
- context: <背景>
- options:
  - A: ...
  - B: ...
  - C: ...
- choice: <选了哪一个>
- reason: <为什么>
- risk: <已知代价 / 何时需要重新评估>
```

### 输出：阶段汇报末尾的日志摘要

```markdown
本阶段新增：A-003、A-004；D-002。
```

## 3. 执行步骤

1. session 开头粘贴输入 A 的规则 prompt。
2. 让 Agent 创建空的 `ASSUMPTIONS.md` 与 `DECISIONS.md`（用输入 B 的骨架）。
3. 每个阶段汇报检查：
   - 是否出现新假设却没编号？→ 让它补 A-NNN。
   - 是否做了关键技术选择却没 D-NNN？→ 让它补，并补 `options` 至少 2 个。
4. 进入 `review-architecture-audit` 时，把这两个文件作为输入；审查报告应能引用具体编号。
5. 进入 Designer Mode 重构时，被推翻的条目不要删，改成「Superseded by D-XYZ」并保留历史。

## 4. 失败处理 / 回滚

- **Agent 在代码里直接做了高风险决定**（如调用外部 API、删除用户数据、引入付费服务）→ 立即 stop，要求还原 + 写入 DECISIONS.md 后重决。
- **日志全是流水账**（每行命名、每行格式都记）→ 要求合并；只记**会影响后续重构**的项。
- **替代方案缺失**（D-NNN 只有 choice 没有 options）→ 拒收，要求至少补 1 个被否决方案 + 否决理由。
- **回滚**：用 `git log -- ASSUMPTIONS.md DECISIONS.md` 回看每次决定；按编号 `git revert` 对应代码 commit。

## 5. 示例

### 示例：D-002 选择本地存储后端

```markdown
## D-002 选择 SQLite 作为本地存储
- date: 2026-04-29
- context: task tracker demo 需要持久化，单用户、单机、< 1 万条
- options:
  - A: JSON 文件全量读写 —— 简单但并发不安全
  - B: SQLite —— 自带文件锁 + SQL 查询
  - C: LevelDB —— KV 模型，需自己做查询
- choice: B
- reason: 数据量与并发都小，但要避免文件损坏；SQLite 单文件部署仍简单
- risk: 引入 native 依赖，跨平台构建复杂；如果未来要换网络存储，仍需抽 Repository 接口（见 D-003）
```

### 示例：A-004 时区假设

```markdown
## A-004 所有时间戳按本机时区存储
- date: 2026-04-29
- where: src/model.ts、src/store.ts
- why: demo 仅本地使用
- impact: 多用户/跨时区同步时会出错
- revisit: 引入远程同步前必须改成 UTC + 显式时区字段
```

## 6. 关联 skill

- 上游 ↖：[`project-manager-bootstrap`](../project-manager-bootstrap/SKILL.md)（启动时声明本规则）
- 下游 ↘：[`review-architecture-audit`](../review-architecture-audit/SKILL.md)（审查报告引用 A/D 编号）
- 配套 ：[`designer-design-card`](../designer-design-card/SKILL.md)（小决策从设计卡片溢出时落到这里）
