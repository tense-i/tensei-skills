---
name: short-insight-deep-dive
description: |
  将短篇洞察、社交媒体短帖、经验片段或一句话忠告扩展成高信息密度的中文深度文档，要求覆盖全部核心观点，并补认知模型、批判性分析、方法论逆向和行动清单。

  Use when: 用户提供短篇文章、短帖、行业洞察、经验总结、领导短评或金句，希望把短内容扩展成可教学、可落地、可转发的深度文档。
user-invocable: true
metadata:
  author: tense-i
  version: "1.0"
  language: "zh-CN"
---

# Short Insight Deep Dive

用这个 skill 处理“篇幅短但观点密度高”的内容，把它扩成完整文档。

## Core goal

在不捏造作者具体事实的前提下，把短内容扩展为：
- 观点全覆盖
- 认知模型还原
- 批判性分析
- 方法论逆向工程
- 行动清单与误区防护

## Defaults

- `must_cover_all_claims`: `true`
- `min_claim_coverage_ratio`: `1.0`
- `min_output_length_rule`: `O >= max(S * 4, 2500)`
- `expansion_priority`: `depth_first`
- `anti_fluff`: `true`
- `fidelity_mode`: `balanced`

## Required workflow

1. 先抽取显性观点和隐性结论，建立 Claim Ledger。
2. 做语义澄清，把金句改写成可讨论命题。
3. 推断来源类型、隐含假设和认知模型。
4. 逐条深挖观点，至少补定义/机制/条件/反例/指标/案例/操作中的 4 项。
5. 输出批判性分析、逆向方法论和行动建议。

## Grounding rules

- 不虚构作者经历或未表达的具体事件。
- 可以补通用案例和背景，但要和作者事实分开。
- 不确定内容标 `【推断】` 或 `【假设】`。
- 长度超限时按 `Part 1 / Part 2 / ...` 连续输出。

## Output

按 `references/output-template.md` 输出。

## Quality bar

- 必须 100% 覆盖核心观点。
- 新增内容必须带来新维度，不能靠空话凑字数。
- 至少给出强反例、适用边界、风险提示和可证伪信号。
- 读者应能据此做决定、做行动或做复盘。

## References

- `references/output-template.md`: 深度文档默认结构
