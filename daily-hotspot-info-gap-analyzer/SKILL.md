---
name: daily-hotspot-info-gap-analyzer
description: |
  将一段口语化、碎片化的每日热点串讲整理成结构化中文分析文档，重点识别热点叙事与关键事实之间的信息差、背景缺口、误读点和后续观察指标。

  Use when: 用户要做每日热点分析、信息差早报、晨会简报、舆情复盘、风险雷达，或提供一段热点口播/碎片文本并要求结构化分析。
user-invocable: true
metadata:
  author: tense-i
  version: "1.0"
  language: "zh-CN"
---

# Daily Hotspot Info-Gap Analyzer

用这个 skill 把热点串讲变成“事实、缺口、影响路径、观察清单”四位一体的分析稿。

## Core goal

对每条热点都回答：
- 发生了什么
- 缺了哪些关键背景或基准
- 影响会怎么传导
- 接下来该盯哪些数据或事件

## Required workflow

1. 先做文本清理，修正断句、数字和单位。
2. 切分热点条目并归类主题。
3. 把“事实”和“观点/推断”分开。
4. 每条热点至少输出两类信息差。
5. 最后汇总今日最大信息差和明日观察清单。

## Grounding rules

- 不得编造事实；未核实内容标 `【口述待核】`、`待核实` 或 `来源不明`。
- 涉及政治或公共卫生，只做事实对照、背景补全和可验证问题，不做煽动性输出。
- 每条热点都要给可证伪的观察指标。

## Output

按 `references/output-template.md` 输出：
- 标题
- TL;DR
- 逐条热点分析
- 全局复盘
- Tomorrow Checklist

## Quality bar

- TL;DR 必须同时包含要点、背景、偏差/缺口、开放问题。
- 每条热点都要有 Facts / Context / Gaps / Watchlist / Open Questions。
- 没看原始输入的读者也应能独立看懂。

## References

- `references/output-template.md`: 固定输出骨架
