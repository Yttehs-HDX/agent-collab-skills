# Skills 索引

按**协作生命周期**分组。建议从 `declare-session-mode` 开始，再依据当前阶段选择对应 skill。

## 安装方式

```bash
npx skills add Yttehs-HDX/agent-collab-skills@<skill-name>
```

加 `-g -y` 可全局静默安装。详见根目录 [README](../README.md#快速开始)。

## declare（声明）

- [declare-session-mode](declare-session-mode/SKILL.md) — 每个 session 开头先声明模式，避免「模式未声明」导致的预期错位。  
  `npx skills add Yttehs-HDX/agent-collab-skills@declare-session-mode`

## design（小步设计）

- [designer-design-card](designer-design-card/SKILL.md) — Designer Mode 的核心：用「设计卡片 + 四把锁」让 Agent 在写代码前先出可讨论的对象。  
  `npx skills add Yttehs-HDX/agent-collab-skills@designer-design-card`

## execute（自主推进）

- [project-manager-bootstrap](project-manager-bootstrap/SKILL.md) — Project Manager Mode 启动 prompt，包含七个控制点，让 Agent 可以自主推进但不失控。  
  `npx skills add Yttehs-HDX/agent-collab-skills@project-manager-bootstrap`
- [stop-and-converge](stop-and-converge/SKILL.md) — 当 Agent 开始扩张或核心闭环已跑通时，把 session 从「继续产出」拉回「完成交付」。  
  `npx skills add Yttehs-HDX/agent-collab-skills@stop-and-converge`

## review（审查）

- [review-architecture-audit](review-architecture-audit/SKILL.md) — Review Mode：让 Agent 停止写代码，输出架构审查报告，作为两种模式之间的缓冲。  
  `npx skills add Yttehs-HDX/agent-collab-skills@review-architecture-audit`

## log（决策可追踪）

- [decision-assumption-log](decision-assumption-log/SKILL.md) — 强制 Agent 在自主推进时把假设和决策落到 `ASSUMPTIONS.md` / `DECISIONS.md`，避免黑箱产物。  
  `npx skills add Yttehs-HDX/agent-collab-skills@decision-assumption-log`

## switch（模式切换）

- [mode-switch-gate](mode-switch-gate/SKILL.md) — 判断当前应该停留还是切到下一个模式的信号清单。  
  `npx skills add Yttehs-HDX/agent-collab-skills@mode-switch-gate`

## 推荐生命周期

```text
Intent → declare(PM) → project-manager-bootstrap
                       ↘ (随时) decision-assumption-log
       → stop-and-converge → review-architecture-audit
       → declare(Designer) → designer-design-card (每片)
       → mode-switch-gate（每里程碑结束自检一次）
```
