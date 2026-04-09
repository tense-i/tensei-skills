---
name: interview-question-bank
description: build a high-frequency, market-aware interview question bank from a role description, jd, or technology direction. use when chatgpt needs to research current hiring demand, official job posts, recruitment platforms, nowcoder interview posts, or public technical communities, then generate a large interview question bank with categories, difficulty, and likely follow-up questions. supports campus recruiting, spring/autumn recruiting, and 1-3 year social recruiting, and should infer the target level automatically from the input.
---

Use this skill to turn a job/technology description into a large, practical interview question bank.

## Core goal
Generate a high-frequency, real-world interview bank that is grounded in current public evidence, not just generic knowledge.

## Required workflow
1. Parse the user input. Accept any of these forms:
   - a one-line role description
   - a full JD
   - a technology/domain keyword such as `webrtc`, `linux driver`, `c++ backend`, `embedded audio-video`
2. Infer the target profile:
   - campus / spring-autumn recruiting
   - junior social recruiting (1-3 years)
   - mixed / unclear
3. Research the current market with web search before generating the bank.
4. Build the question bank from evidence.
5. Clearly separate sourced questions from synthesized questions.

## Research rules
Always browse the web unless the user explicitly forbids it.

### Source priority
Prioritize sources in this order:
1. official company career pages and campus recruiting pages
2. nowcoder job posts, interview experience pages, written test pages
3. recruitment platforms such as boss, zhilian, 51job, lagou, liepin, haitou
4. credible technical communities or blogs for domain-specific interview patterns

### Search strategy
Use multiple parallel queries. Combine:
- the role/domain keyword
- current year or current recruiting season
- terms such as `校招`, `春招`, `秋招`, `社招`, `岗位要求`, `面经`, `真题`, `笔试`, `面试`
- domain-specific terms such as `ffmpeg`, `webrtc`, `rtp`, `linux驱动`, `bsp`, `camera isp`, `stl`, `多线程`

Search for both:
- current job demand (what employers explicitly ask for)
- real interview experiences or question collections (what candidates report being asked)

### Evidence threshold
Before generating the final bank, gather enough public evidence to support the output. As a rule of thumb:
- use at least 6 high-quality sources for narrow topics
- use at least 10 high-quality sources for broader roles
- prefer recent sources for hiring demand
- do not rely on a single blog or a single anecdote

### Freshness and trust
- treat official career pages and recent job posts as the strongest evidence for current demand
- treat public interview posts as evidence for question style, not universal truth
- if a question appears repeatedly across multiple sources, treat it as high-frequency
- if a question is synthesized from market demand rather than directly quoted from a source, label it as synthesized

## Output requirements
Respond in Chinese unless the user asks otherwise.

### Output structure
Use this structure unless the user requests a different format:

# [岗位/技术] 面试题库

## 1. 画像判断
- 目标岗位/技术方向
- 推断适用人群（校招 / 春秋招 / 1-3 年社招）
- 当前市场重点

## 2. 检索结论
- 当前招聘侧重点
- 高频知识模块
- 真实面经/真题来源概况

## 3. 大题库
Split the bank into these sections when relevant:
- 基础题
- 拔高题
- 异常排查 / bug 定位题
- 场景题 / 系统设计题
- 热门题 / 当下高频题
- 性能优化题
- 大厂真实题 / 面经题

Within each section, list many questions. For every question include:
- 分类
- 难度（初 / 中 / 高）
- 题目
- 高频追问点
- 来源属性：`真实来源` or `综合抽象`

Recommended per-item format:
- **题目**：...
- **难度**：初 / 中 / 高
- **高频追问点**：1) ... 2) ...
- **来源属性**：真实来源 / 综合抽象

## 4. 热门程度总结
Call out:
- 必问题
- 最容易深挖的题
- 最能拉开差距的题

## 5. 参考来源
Summarize the public sources used, with citations.

## Sourcing rules for questions
- Only mark a question as `真实来源` if it is clearly grounded in an interview post, written test post, or public question collection.
- If the final wording is merged, normalized, or generalized from several sources, mark it as `综合抽象`.
- Do not claim a question came from a specific company unless the source actually supports that claim.

## Quality bar
The bank must be:
- broad enough to cover fundamentals and domain specialization
- practical enough to include debugging, failure handling, production issues, and trade-offs
- current enough to reflect recent hiring demand
- honest about what is directly sourced vs synthesized

## Special guidance by role type
- For campus / spring-autumn recruiting, increase weight on fundamentals, written-test style questions, and project deep dives.
- For 1-3 year social recruiting, increase weight on troubleshooting, performance, engineering trade-offs, architecture, and production incidents.
- For embedded roles, increase weight on linux, drivers, bsp, buses, rtos, bringup, reliability, and hardware-software debugging.
- For audio-video roles, increase weight on codecs, transport protocols, weak-network handling, sync, jitter, ffmpeg/webrtc, and latency/stability optimization.
- For c++ roles, always include memory model, object model, templates/stl, concurrency, and performance reasoning.

## Failure handling
If public evidence is thin:
- say so explicitly
- still provide a useful bank
- reduce certainty labels
- explain which parts are directly sourced and which are synthesized from adjacent evidence
