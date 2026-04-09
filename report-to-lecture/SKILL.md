---
name: report-to-lecture
description: |
  将文章、研报、论文或白皮书重写为可教学、可转发、可落地的中文讲义，要求高覆盖率、高保真和教学化扩展，并在需要时输出原子信息点 Ledger 与 Claim-Evidence 映射。

  Use when: 用户要求精读文章、研报解读、论文讲解、内部培训讲义、读书会材料，或强调“不要过度总结”“按讲义方式讲清楚”。
user-invocable: true
metadata:
  author: tense-i
  version: "1.0"
  language: "zh-CN"
---

# Report to Lecture

用这个 skill 把长文材料整理成讲义，而不是摘要。

## Core goal

在不篡改原文事实的前提下，把原文重组为可教学结构，并补齐：
- 定义
- 机制
- 推导链路
- 边界条件
- 落地方法

## Defaults

默认护栏：
- `fidelity_mode`: `balanced`
- `min_length_ratio`: `0.8`
- `min_coverage_ratio`: `0.8`
- `coverage_granularity`: `medium`
- `expansion_depth`: `standard`

## Required workflow

1. 先拆文档结构：章节、图表、关键结论段。
2. 建两张账本：Coverage Ledger、Claim-Evidence Map。
3. 按教学顺序重排内容：背景 -> 框架 -> 证据链 -> 深入讲解 -> 落地。
4. 对跳步结论补机制、边界和应用，但要标 `【推断】` 或 `【通用】`。
5. 输出前检查字数下限和覆盖率，不够就补关键点而不是套话。

## Grounding rules

- 原文数字、范围、时间、限定条件不得抹平。
- 推断与评论必须显式区分，不能伪装成原文事实。
- 尽量避免长引文；引用以定位和证据锚定为主。
- 长文输出不够时按 `Part 1 / Part 2 / ...` 连续输出。

## Output

按 `references/output-template.md` 输出讲义主体。

在这些场景额外读取：
- 金融研报：`references/finance-addon.md`
- 学术论文：`references/paper-addon.md`
- 需要更细覆盖判定：`references/ledger-rules.md`

## Quality bar

- 不能只写 TL;DR。
- 每个大节至少要有“结论 + 解释 + 示例/边界/方法”。
- 讲义读者不看原文也要能读懂。
- 覆盖率不足时必须补齐缺失点。

## References

- `references/output-template.md`: 默认讲义结构
- `references/ledger-rules.md`: 信息点拆分与覆盖判定
- `references/finance-addon.md`: 研报专项补充
- `references/paper-addon.md`: 论文专项补充
