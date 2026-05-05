---
name: interview-question-bank
description: generate production-grade interview question banks from a role, jd, technology direction, project, or business system. use when the user asks for 面试题库, 高频题, 面经题, 项目深挖题, 系统设计题, or interview preparation for backend, distributed systems, java, redis, mq, database, payment, flash sale, inventory, order, membership, or other business systems. prioritize real production problems, business constraints, technical decision points, failure modes, follow-up chains, answer anchors, evaluation rubrics, and evidence from current jd/interview sources instead of shallow question lists.
---

# Interview Question Bank

## Purpose

Create a high-depth, practical interview question bank that trains the user to answer like an engineer who has operated real systems, not like someone reciting a demo. The output must connect business context, architecture decisions, production failures, trade-offs, observability, and implementation constraints.

## Core principles

- Start from the business scenario and system invariants before listing questions.
- Prefer production questions over textbook questions: failures, race conditions, compensation, pressure testing, data inconsistency, degraded modes, and operational playbooks.
- Do not output a flat list of generic questions. Every important question must include a follow-up chain and answer anchors.
- Separate directly sourced questions from synthesized production-depth questions.
- For project-based questions, simulate how a senior interviewer will attack weak claims: scale numbers, bottlenecks, edge cases, data consistency, and proof.
- Treat “high frequency” and “high value” separately: some common questions are basic; the best bank must also include questions that differentiate strong candidates.

## Required workflow

### Step 1 — Parse the input

Accept any of these inputs:
- role or JD, such as `java backend 1-3 years`
- technology direction, such as `redis`, `mq`, `distributed transaction`
- project/system, such as `秒杀系统`, `支付路由`, `库存中心`, `订单履约`
- user-provided architecture notes, incident notes, or previous analysis

Infer and state:
- target interview level: campus / 1-3 years / 3-5 years / senior / unclear
- target interview style: foundation-heavy / project-deep-dive / system-design / troubleshooting / mixed
- business domain and core invariants
- assumed technology stack, only when the user did not specify one

### Step 2 — Research current evidence

Always browse unless the user explicitly forbids browsing.

Collect evidence from:
1. official company career pages and JDs
2. nowcoder / interview experience / written test posts
3. recruitment platforms and public job descriptions
4. credible technical blogs, engineering articles, and system design resources

Use evidence for two separate things:
- hiring demand: what employers ask candidates to know
- interview style: how questions are actually asked and followed up

If evidence is thin, say so and lower certainty. Still synthesize useful production questions from the business/system constraints.

### Step 3 — Build a production issue ledger

Before generating the bank, create or mentally maintain a ledger of production issues the system must face. For each system/project, include domain-specific issues, not just generic middleware topics.

For a flash sale system, the ledger must include questions such as:
- Lua 脚本如何保证校验和扣减的原子性？
- 库存什么时候从数据库预热到 Redis？
- 活动还没开始就有请求进来怎么办？
- Redis 库存和数据库库存不一致怎么补偿？
- 扣库存成功但创建订单失败怎么办？
- 先预扣库存再发 MQ，还是先发 MQ 再预扣？
- Redis 排队视图是不是事实源？
- 支付成功但订单同步失败怎么办？
- 支付成功但发卡/发货失败怎么办？
- 队列积压时扩容还是关入口？

For other systems, build an equivalent ledger around that domain’s true production risks.

### Step 4 — Identify decision points

Extract 8-20 technical decision points. A decision point is not a component name. It is a fork where different choices create different failure modes.

Good decision-point wording:
- 库存裁决放在入口还是消费端？
- 支付成功后直接同步履约，还是先落本地事件再异步履约？
- Redis 是事实源还是查询视图？
- MQ 按商品拆队列还是统一队列？

Bad wording:
- Redis
- MQ
- MySQL
- 限流

### Step 5 — Generate layered question bank

Use the default output structure below. Adapt section names to the role/system, but keep the depth requirements.

For each important question include:
- classification
- difficulty: 初 / 中 / 高 / 专家
- interviewer intent: what the interviewer is really testing
- question
- follow-up chain: at least 3 progressive follow-ups for medium/high questions
- answer anchors: key points a strong answer should mention
- production pitfall: what demo-level candidates miss
- source attribute: `真实来源` / `综合抽象`

For simple foundation questions, a shorter item is acceptable. For core production questions, use the full format.

## Default output structure

# [方向/系统] 面试题库

## 1. 画像判断
- 目标岗位/技术/系统
- 推断适用人群
- 面试官会如何深挖
- 当前市场/面试重点

## 2. 业务与系统背景
Explain the business goal, user journey, core modules, hard invariants, and failure consequences. For business systems, include a short “系统不是 demo 的原因”.

## 3. 生产问题账本
List the real online problems the candidate must be able to reason about. This section is mandatory for system/project question banks.

Use this format:
| 问题 | 为什么线上一定会遇到 | 面试会怎么问 | 低质量回答特征 |

## 4. 技术决策地图
List the P0/P1/P2 decision points.

Use this format:
| 优先级 | 决策点 | 它决定什么 | 选错后果 | 关联题号 |

## 5. 大题库
Split into relevant sections. For backend systems, default sections:
- A. 开场与业务理解题
- B. 入口、防刷、限流与准入题
- C. 缓存、Redis 视图与一致性题
- D. 库存/资源裁决与原子性题
- E. MQ、异步化与削峰题
- F. 订单/状态机/数据模型题
- G. 支付/履约/补偿题
- H. 故障排查与事故复盘题
- I. 压测、监控与容量评估题
- J. 架构取舍与系统设计题

Core question item template:

### Q[number]. [question title]
- **分类**：...
- **难度**：初 / 中 / 高 / 专家
- **面试官意图**：...
- **题目**：...
- **追问链**：
  1. ...
  2. ...
  3. ...
- **强答案锚点**：
  - ...
  - ...
- **生产坑点**：...
- **来源属性**：真实来源 / 综合抽象

## 6. 深挖题专题包
Choose 5-8 topics most likely to separate candidates. Each topic must be a mini interview path, not a single question.

For each topic:
- 起手问题
- 第一层追问: implementation detail
- 第二层追问: failure mode
- 第三层追问: trade-off
- 第四层追问: proof/metrics
- 合格答案结构
- 优秀答案加分点
- 危险回答

## 7. 评分 Rubric
Provide a scoring guide:
- 60 分: can describe components
- 75 分: can explain mechanisms and basic failures
- 85 分: can discuss trade-offs and invariants
- 95 分: can reason about production incidents, metrics, and recovery

## 8. 复习路线
Group questions by preparation priority:
- 必背
- 必会推理
- 项目深挖
- 事故排查
- 高阶拉分

## 9. 参考来源
Cite public sources used. Do not cite unsupported claims.

## Quality requirements

A high-quality output must satisfy all checks:
- Includes business context, not only middleware keywords.
- Includes at least 8 technical decision points for system/project topics.
- Includes production issue ledger.
- At least 30% of questions must be high-depth production questions with answer anchors and pitfalls.
- Includes failure handling questions for every async boundary.
- Includes consistency questions for every state transition that affects money, inventory, order, or entitlement.
- Includes observability and pressure-test questions.
- Clearly distinguishes sourced vs synthesized.
- Avoids shallow wording such as “Redis 如何用” unless followed by concrete scenario and failure mode.

## Flash sale depth requirements

When the topic is 秒杀 / flash sale / seckill, include these decision and failure areas:

1. business semantics
- 入队成功、抢购成功、待支付、支付成功、发货/发卡成功分别是什么意思？
- 哪些状态必须强一致，哪些可以最终一致？

2. entrance and anti-abuse
- 活动未开始请求、隐藏接口、动态 token、用户级限流、IP/设备限流、黑名单
- 真人体验和防刷强度如何取舍

3. redis queue view
- Redis 排队视图存什么，TTL 如何设
- Redis 视图和事实源如何区分
- Redis 写成功但 MQ 失败如何修复

4. inventory arbitration
- Lua 原子边界
- 限购和库存扣减是否合并
- 库存预热时间点
- DB 库存、Redis 库存、冻结库存、已售库存如何对账

5. mq and async boundaries
- 先预扣再发 MQ vs 先发 MQ 再预扣
- 按 SKU/商品拆队列
- 重复消费、消息丢失、死信、积压治理

6. order and payment
- 秒杀订单中心是否独立
- 支付成功后同步普通订单域
- 支付成功但订单同步失败
- 支付超时释放库存

7. fulfillment and compensation
- 虚拟商品发卡失败
- 重复发卡防护
- 补偿 MQ / 定时任务 / 人工兜底

8. operations
- 压测如何覆盖抢购、轮询、支付、回调、同步、发卡
- 监控哪些指标决定关入口、降速、补偿

## Bad output patterns to avoid

Do not produce only this style:
- “Redis 如何防超卖？”
- “MQ 有什么作用？”
- “如何限流？”

Instead produce scenario-specific versions:
- “Lua 脚本里同时做限购判断、库存判断、扣减和用户占位，哪些必须在同一个原子边界里？如果拆成多个 Redis 命令，会出现什么中间状态？”
- “Redis 预扣库存成功后，MQ 发送超时但实际 broker 已收到，入口应该回滚库存吗？如何避免既超卖又少卖？”
- “支付成功后普通订单同步失败，用户端展示什么状态？后台如何证明这笔钱最终对应到订单和权益？”

## Source labeling rules

- Mark as `真实来源` only if the question is clearly present in an interview post, written test page, job post, or question collection.
- Mark as `综合抽象` when the wording is synthesized from production constraints, architecture notes, or multiple sources.
- Do not claim a question belongs to a specific company unless the cited source explicitly supports it.
