---
name: github-trending-quick-deep-analyzer
description: |
  对 GitHub Trending 仓库或单个 GitHub repo 输出逐仓库同等深度的中文分析，覆盖详细 TL;DR、核心特性、痛点到方案到效果、证据来源和快速试用路径。

  Use when: 用户提到 GitHub Trending、热门仓库、想快速读懂某个 repo、想知道它解决什么问题、为什么火、有哪些核心特性，或要求做榜单速报。
user-invocable: true
metadata:
  author: tense-i
  version: "1.0"
  language: "zh-CN"
---

# GitHub Trending Quick Deep Analyzer

用这个 skill 快速读懂 Trending 仓库，重点是“仓库是什么、怎么试、解决了什么痛点、证据在哪里”。

## Core goal

对每个仓库都给出三类稳定信息：
- 详细 TL;DR
- 核心特性，且每条都带场景和最小示例
- 痛点 -> 方案 -> 效果，并说明如何验证

## Defaults

用户没说时使用：
- Trending 周期：`daily`
- 语言：不限
- `N`：`5`
- 输出语言：中文

## Required workflow

1. 判断是单仓库分析还是 Trending Top N。
2. 采集至少三类公开证据：repo 首页、README、docs/release/issues 中的任意一类补充。
3. 先提炼仓库定位和核心价值，再整理特性与问题链路。
4. 多仓库时，对每个仓库使用同一模板和同一最低信息量。
5. 输出前补充实际看过的证据入口。

## Retrieval rules

- 优先级：Repo 首页 > README > Docs/Website > Releases/Changelog > Issues/PR > Trending 页面。
- 关键结论要尽量能指回公开资料；没有证据就写“未在公开资料中确认”。
- 多仓库模式下，不允许只深挖前几个仓库。
- 对每个仓库至少给出一个最小示例；没有现成示例时可写 `【示意】`。

## Output

按 `references/output-template.md` 输出。每个仓库必须包含：
- 元信息
- 详细 TL;DR
- 核心特性
- 痛点 -> 方案 -> 效果
- 证据来源

## Quality bar

- 每个仓库都要同等详细，不允许“剩余略”。
- TL;DR 不能空泛，必须有明确用户、场景、风险。
- 特性部分必须帮助读者“看完就知道怎么试一下”。
- 痛点部分必须包含验证方式或指标。

## References

- `references/output-template.md`: 逐仓库输出模板与自检要求
