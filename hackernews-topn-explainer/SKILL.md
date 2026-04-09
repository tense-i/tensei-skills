---
name: hackernews-topn-explainer
description: |
  抓取 Hacker News 的 top/best/new/ask/show/jobs 榜单，针对 Top N 帖子输出结构化中文深读文档，解释帖子讲了什么、为什么上榜、讨论焦点和可执行结论。

  Use when: 用户提到 Hacker News、HN、热榜、Top N、今日 HN、上热搜、为什么火，或要求把 HN 榜单做成中文简报/讲解文档。
user-invocable: true
metadata:
  author: tense-i
  version: "1.0"
  language: "zh-CN"
---

# Hacker News TopN Explainer

用这个 skill 把 Hacker News 榜单变成可直接转发的中文深读文档。

## Core goal

对 HN Top N 的每一条帖子做同等深度分析，回答四件事：
- 它在讲什么
- 为什么能上榜
- 评论区在争论什么
- 不同角色接下来该怎么跟进

## Defaults

用户没给参数时使用：
- 榜单类型：`top`
- `N`：`20`
- 输出语言：中文
- 深度策略：所有条目同结构、同最低信息量

## Required workflow

1. 先确认分析对象是单帖还是榜单。
2. 通过 HN 官方 Firebase API 抓取 story 列表与详情。
3. 对每条 story 补充抓取原文页；如果是 Ask/Show，则以站内正文和评论为主。
4. 抽样评论，必要时用 Algolia HN Search API 补评论聚类或趋势信号。
5. 先写全局总览，再逐条输出完整分析。
6. 如果长度超限，只能分 Part 输出，不能降低单条完整度。

## Retrieval rules

- 始终以实时抓取结果为准，不凭记忆描述“今天/当前”榜单。
- “发生了什么”优先看原文页、Show HN 项目页或 Ask HN 正文。
- “为什么上榜”优先用可观察信号：`score`、评论量、发布时间、话题契合度、发布节点、站点影响力。
- 无法确认的内容必须标 `【未确认】` 或 `【推测】`。
- 对每条帖子至少覆盖：内容主线、一个关键细节、一个背景补充、一个可验证点。

## Quality bar

- 禁止“前几条详、后几条略”。
- 每条都必须解释“它在讲什么”，且不是只复述标题。
- 每条都要给行动建议，且至少一条建议带最小验证步骤。
- Ask/Show/无外链/付费墙等特殊情况要显式标注。

## Output

按 `references/output-template.md` 输出：
- 先写当日总览
- 再按统一模板逐条分析
- 每条都保留元信息、TL;DR、展开说明、讨论焦点、行动建议、风险与偏差

## References

- `references/output-template.md`: 默认输出结构与字段要求
