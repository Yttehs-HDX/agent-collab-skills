# 贡献规范

欢迎贡献新的 skill 或改进现有 skill。

## 一个 skill 的最小标准

新增 skill 必须满足：

1. **YAML frontmatter 齐全**（`name` + `description`）。这是 `npx skills` CLI 检索和安装的依据；缺失或字段名错误会让 skill 无法被发现。`name` 必须与目录名一致。
2. 有清晰的**适用边界**（什么时候用 / 什么时候不用）。
3. 有**可复制的输入输出格式**（prompt 片段 / 卡片模板 / 文件结构）。
4. 步骤是**可执行**的，不是抽象建议。
5. 给出**失败处理或回滚**方式。
6. 至少 1 个**完整示例**。

### Frontmatter 示例

```yaml
---
name: my-skill-name
description: One sentence on what it does + when to use it. Mention the trigger keywords and situations the user is likely to ask about, since this string is what skills.sh search ranks against.
---
```

`description` 写得越具体、触发词越多，越容易被 `npx skills find` 命中。

## 添加新 skill

1. 复制 [templates/SKILL_TEMPLATE.md](templates/SKILL_TEMPLATE.md) 到 `skills/<kebab-case-name>/SKILL.md`。
2. 按模板填写所有必填段。
3. 在 [skills/index.md](skills/index.md) 中加入对应主题分组。
4. 如果 skill 引用了原始笔记中没有的事实，请在 PR 描述中标注来源。

## 目录约定

```
skills/<skill-name>/
  SKILL.md          # 必填
  scripts/          # 可选：脚本（保持最小依赖）
  assets/           # 可选：图片、示例文件
  references/       # 可选：参考资料、原笔记摘录
```

## 命名

- skill 目录使用 kebab-case，例如 `designer-design-card`。
- 名称体现**动作或工件**（`declare-session-mode`），而不是抽象名词。

## 风格

- 中英混排时，中文与英文/数字之间留一个空格。
- 引用原笔记时，注明来源章节，避免脱离上下文重新发明。
- 不引入与 skill 无关的技术栈（保持仓库纯文档为主）。

## PR 检查清单

- [ ] 新 skill 的 `SKILL.md` 顶部有合法的 YAML frontmatter（`name` 等于目录名 + `description` 触发词丰富）
- [ ] 6 段（目的 / 输入输出 / 步骤 / 失败处理 / 示例 / 关联 skill）齐全
- [ ] 已加入 `skills/index.md`（含安装命令）
- [ ] README 不需要更新（或已更新）
- [ ] 没有改动 `docs/` 下的原始笔记
- [ ] 本地至少跑一次 `npx skills find <你的关键词>` 自验能命中
