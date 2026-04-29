---
name: <kebab-case-skill-name>
description: <One sentence on what this skill does, plus when to use it. The `npx skills` CLI surfaces this string in search results, so it must be self-contained and trigger-rich (mention the situations and keywords that should activate the skill).>
---

# <Skill 名称>

> 主题分组：declare / design / execute / review / log / switch / harden  
> 来源：（如来自原始笔记，注明章节）

> 注意：上面的 YAML frontmatter 是 `npx skills` CLI 必需的。`name` 必须与目录名一致；`description` 用于检索与命中判定，写得越具体越容易被命中。

## 1. 目的与适用边界

- **目的**：一句话说清这个 skill 解决什么问题。
- **适用**：在哪些场景下使用。
- **不适用**：明确不要在哪些场景使用，避免误用。

## 2. 输入 / 输出

### 输入（粘贴给 Agent 的内容）

```markdown
<可复制的 prompt 模板或卡片格式>
```

### 输出（期望 Agent 返回的内容）

```markdown
<期望产物的结构，例如设计卡片、阶段汇报、审查报告>
```

## 3. 执行步骤

1. 步骤一（可执行，不抽象）
2. 步骤二
3. 步骤三

## 4. 失败处理 / 回滚

- 失败信号：当出现 X 时说明 skill 失效。
- 回滚动作：如何撤回 / 切换到另一个 skill。

## 5. 示例

### 示例 1：<场景描述>

**输入**：

```markdown
...
```

**输出**：

```markdown
...
```

## 6. 关联 skill

- 上游：在哪个 skill 之后使用
- 下游：之后通常切换到哪个 skill
