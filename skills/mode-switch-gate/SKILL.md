---
name: mode-switch-gate
description: Decide whether to stay in the current Agent collaboration mode or switch (Intent -> PM -> Review -> Designer -> Harden -> PM-experiment) using a self-check card and a signal table. Use after every milestone or progress report, or whenever something feels off but you cannot articulate why.
---

# mode-switch-gate

> 主题分组：switch  
> 来源：`docs/03-两种 Agent 使用模式的协作与切换.md` §4「推荐生命周期」、§5「切换门」

## 1. 目的与适用边界

- **目的**：判断**当前是否应该切换模式**，并给出切换的具体动作。让生命周期 `Intent → PM → Review → Designer → Harden → (PM)` 在每个项目里可执行，而不是凭感觉。
- **适用**：每次阶段汇报或里程碑之后；觉得「不对劲但说不出来」的时候。
- **不适用**：单次问答 / 单次代码片段，不存在阶段切换。

## 2. 输入 / 输出

### 输入：自检卡片（人填，不丢给 Agent）

```markdown
# Mode Switch Self-check

- 当前 mode：
- 当前可运行物：<能跑的最小命令 / 入口>
- 最近一次阶段汇报：<时间 / 摘要>
- 我现在能在脑中描述主要模块吗？(y/n)
- ASSUMPTIONS / DECISIONS 是否最新？(y/n)
- 我有没有连续两次想说「顺便也做 X」？(y/n)
```

### 输出：切换决定 + 下一动作

```markdown
- 切换目标：<同 mode / Review / Designer / Harden / PM-局部实验>
- 切换动作：<粘贴哪个 skill 的 prompt>
- 切换前要做的事：<commit / 收束 / 出报告 等>
```

## 3. 执行步骤

1. 填自检卡片。
2. 对照「切换信号表」（§3.1）找到匹配信号。
3. 按「切换动作表」（§3.2）执行；**绝不在同一 session 里默默换 mode**。
4. 切换前先 commit 当前可运行 checkpoint，留好回滚点。
5. 新 session 用 `declare-session-mode` 重新声明。

### 3.1 切换信号表

| 当前 mode | 出现什么 → | 切到 |
| --- | --- | --- |
| PM | 项目能本地启动 / 核心路径走通 / Agent 开始建议加高级功能 / 我已经描述不出主要模块 | **Review** |
| Review | 已经回答完 10 段审查报告 / 已选出 5 个最小重构 checkpoint | **Designer** |
| Designer | 一片完成且可验证 / 想试一个新方向但不污染 core | 继续 Designer 下一片 / 局部 **PM**（实验分支） |
| Designer | 核心抽象稳定 / 缺测试 / 缺日志 / 缺错误处理 | **Harden** |
| Harden | 测试 / 日志 / 文档目标达成 | 视情况：Designer（下一片）/ 关闭里程碑 |
| 任意 | Agent 改动失控 / diff > 3 文件未解释 / 我审不动了 | **先 `stop-and-converge`**，再判断 |

### 3.2 切换动作表

| 切到 | 动作 |
| --- | --- |
| Review | `stop-and-converge` 收束 → `review-architecture-audit` |
| Designer | `declare-session-mode`(Designer) → `designer-design-card` |
| PM（局部实验） | 切实验分支 → `declare-session-mode`(PM) → `project-manager-bootstrap` |
| Harden | `declare-session-mode`(Harden)，限定文件范围；本仓库未单独建 skill，按 README 模式表执行 |

## 4. 失败处理 / 回滚

- **频繁切换无产出** → 一周内切换 ≥ 3 次说明 scope 没切薄；回到 Designer，把当前片再切一半。
- **跳过 Review 直接 Designer** → demo 中的临时结构会被误当成正式架构；回退到「Review 报告产出」这一步。
- **同一 session 内偷偷换 mode** → 表现：Agent 一会儿等确认一会儿自主推进；强制结束 session，重发声明卡片。

## 5. 示例

### 示例：task tracker demo 跑通后的判断

**自检**：

```markdown
- 当前 mode：Project Manager
- 当前可运行物：`task add / list / done` 全通过
- 最近汇报：阶段 4，已 commit
- 能描述主要模块？y（cli / store / model）
- ASSUMPTIONS / DECISIONS 最新？y（A-001..A-004，D-001..D-002）
- 想说「顺便也做 X」？y（想加同步、加 UI）
```

**切换决定**：

```markdown
- 切换目标：Review
- 切换动作：
  1) 粘贴 stop-and-converge 输入 C
  2) 收束清单 commit
  3) 新开 session：declare-session-mode(Review) + review-architecture-audit
- 切换前要做：把工作树清干净；确保 ASSUMPTIONS/DECISIONS 已 push
```

## 6. 关联 skill

- 配套 ：[`declare-session-mode`](../declare-session-mode/SKILL.md)（每次切换都要重声明）
- 上游 ↖：任意正在进行的模式
- 下游 ↘：根据决定结果跳到对应 skill（[`project-manager-bootstrap`](../project-manager-bootstrap/SKILL.md) / [`designer-design-card`](../designer-design-card/SKILL.md) / [`review-architecture-audit`](../review-architecture-audit/SKILL.md) / [`stop-and-converge`](../stop-and-converge/SKILL.md)）
