---
name: technical-decision-points
description: analyze technical decision points in a business scenario or system design. use when the user asks to identify, compare, review, or document key technical decisions for systems such as flash sale, payment, order fulfillment, inventory, messaging, recommendations, workflows, risk control, or other business systems. especially useful for turning a scenario into decision points, trade-off matrices, failure modes, recommended choices, architecture constraints, and coding-agent-ready implementation guidance.
---

# Technical Decision Points

## Purpose

Use this skill to turn a business scenario or system design into a structured set of **technical decision points**. The goal is not to list technologies, but to expose the architecture forks that determine correctness, scalability, reliability, cost, and operational risk.

A good output should answer:

- What decision is being made?
- Why does this decision matter to the business scenario?
- What are the viable options?
- What breaks if we choose poorly?
- Which option is recommended under the stated constraints?
- What invariants, failure modes, and acceptance checks must be preserved?

## Input handling

Accept any of these inputs:

- A business scenario, such as `秒杀系统`, `优惠券中心`, `支付路由`, `订单履约`, `IM`, `库存中心`, `推荐系统`.
- A pasted architecture article or design note.
- Existing modules, sequence diagrams, code snippets, PRD text, or operational constraints.
- A request such as `分析这个系统有哪些关键技术决策点`, `帮我拆技术取舍`, `把这个场景变成架构决策文档`.

If the user provides only a scenario name, proceed with explicit assumptions. Do not block on clarification unless the missing information changes the top-level business goal.

Use evidence labels when helpful:

- `[事实]` user-provided facts
- `[推断]` reasonable assumptions from the scenario
- `[待确认]` decisions that require product/business/technical owner confirmation
- `[建议]` recommended choice by the assistant

## Core workflow

### Step 1 — Frame the business scenario

Start with a concise framing:

1. One-sentence scenario definition.
2. Business goal.
3. Critical actors.
4. Main transaction or value flow.
5. Scale and reliability assumptions.
6. What must not be broken.

Example:

```text
秒杀系统不是普通下单接口的高并发版本，而是一条围绕稀缺库存分配建立的交易侧路。核心目标是保护主交易链路，同时保证不超卖、不重复支付、支付成功后最终履约。
```

### Step 2 — Identify decision surfaces

Scan the system through these lenses:

1. **Traffic placement** — where does peak traffic land: gateway, cache, queue, database, or downstream services?
2. **Truth source** — which component owns the durable fact for inventory, order, payment, fulfillment, or user state?
3. **Consistency boundary** — which parts require strong consistency, and which can be eventually consistent?
4. **Async boundary** — where should the system stop synchronous work and switch to events, queues, or jobs?
5. **Idempotency boundary** — which operations may be repeated and what key prevents duplicate side effects?
6. **Isolation boundary** — which traffic or data must be separated from the normal path?
7. **Failure recovery** — how are failures detected, retried, compensated, reconciled, or manually repaired?
8. **Operational control** — what switches, rate limits, dashboards, and runbooks are needed during incidents?

Consult `references/decision-taxonomy.md` for a fuller taxonomy.

### Step 3 — Convert each decision into a decision card

For every important decision point, use this structure:

```markdown
## D{n}. [decision name]

### What is being decided?
[Precise architecture fork, not a vague technology question.]

### Why it matters
[Business impact, scale impact, correctness impact, operational impact.]

### Options
| Option | Mechanism | Strengths | Costs / risks | Best fit |
|---|---|---|---|---|

### Failure modes if chosen poorly
- [Specific accident or degradation path]

### Recommended choice
[Clear recommendation under the current assumptions.]

### Invariants to protect
- [Invariant 1]
- [Invariant 2]

### Validation / acceptance checks
- [Metric, test, chaos case, reconciliation rule, or proof point]
```

Avoid generic decision cards like `use redis` or `use mq`. Rewrite them into sharper decisions:

- Bad: `whether to use mq`
- Good: `where to place the async boundary: before inventory arbitration or after inventory freeze`

- Bad: `redis design`
- Good: `whether redis is the source of truth or only a transient state projection`

- Bad: `database sharding`
- Good: `which sharding key preserves dominant query paths while avoiding hot activity partitions`

### Step 4 — Prioritize decisions

Not every choice deserves equal depth. Classify each decision:

| Priority | Meaning | Typical examples |
|---|---|---|
| P0 | Breaks correctness or money/assets if wrong | inventory over-sell, payment idempotency, order uniqueness, fulfillment duplication |
| P1 | Breaks availability or isolation if wrong | queue partitioning, traffic admission, DB protection, sync/async boundary |
| P2 | Increases cost or operational pain if wrong | dashboard design, cache TTLs, retry intervals, sharding convenience |
| P3 | Implementation detail | framework libraries, local class structure, minor API shape |

Spend most depth on P0 and P1.

### Step 5 — Separate business decisions from technical decisions

Some choices look technical but are business decisions in disguise. Mark them as `[待确认]` when needed:

- Is it acceptable to show users `排队中` before knowing whether they won?
- Should released unpaid inventory return to the pool or remain closed?
- Is delayed fulfillment acceptable after payment success?
- Is oversell absolutely forbidden, or can later refund compensate?
- Should normal orders be protected even if flash-sale conversion drops?

Technical design must not silently decide these.

### Step 6 — Produce the report

Use `references/report-template.md` as the default structure. Adapt section names when the user requests a shorter or more article-like output.

The report should include:

1. Scenario framing.
2. Decision map.
3. Ranked decision list.
4. Deep decision cards.
5. Cross-decision interactions.
6. Recommended architecture posture.
7. Failure modes and runbook implications.
8. Implementation prompts or handoff notes when useful.

### Step 7 — Add coding-agent harness when requested or useful

When the user wants implementation guidance, append a concise prompt harness:

```markdown
# Implementation Harness

## Global constraints
- Preserve these invariants: ...
- Do not bypass these decisions: ...
- Treat these states as eventually consistent: ...

## Module prompts
### M1 — [module]
Responsibilities, inputs, outputs, side effects, idempotency keys, tests.

## Task prompts
### T1 — [task]
Concrete deliverable, acceptance checks, forbidden shortcuts.
```

## Decision quality rules

- Prefer decision questions over technology labels.
- Explain the mechanism, not just the option name.
- Always state what can fail and how the system recovers.
- Distinguish `fast path`, `slow path`, `failure path`, and `compensation path`.
- Do not call eventual consistency safe unless there is retry, idempotency, reconciliation, and observability.
- Do not let cache, queue, or local memory become an accidental source of truth.
- For money, inventory, quota, entitlement, or identity, require durable facts and idempotent transitions.
- For each async boundary, require a message key, retry rule, idempotency rule, and dead-letter or compensation path.
- For each state machine, define terminal states and forbidden transitions.
- For each degradation switch, define what it protects and what user/business capability it sacrifices.

## Common anti-patterns to call out

- Treating `request accepted` as `business success`.
- Treating Redis display state as durable truth.
- Using MQ without idempotent consumers.
- Relying on one global rate limiter instead of resource-specific limits.
- Creating orders before scarce-resource arbitration.
- Calling remote services inside long database transactions.
- Hiding payment or fulfillment failure behind generic `system error` states.
- Designing retries without maximum attempts, dead letter, or manual repair.
- Saying `eventual consistency` without proving eventual convergence.

## References

- `references/decision-taxonomy.md` — decision lenses and common decision categories.
- `references/report-template.md` — default output report structure.
- `references/quality-checklist.md` — self-check before final output.
