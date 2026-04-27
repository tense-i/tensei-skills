# scenario-harness-architect

> 把一个业务场景题(秒杀 / IM / 推送 / 订单履约 / 支付清结算 …)拆成 **7 份分析文档 + 1 份人类判断文档 + 一套分层 harness prompt 包**,让 Claude Code、Codex 这类 coding agent 可以按 prompt 把系统编码落地,同时避免把关键判断外包给 AI。

## 这个 skill 解决什么问题

传统做系统设计,产出通常是一份架构文档,然后再由程序员逐行写代码。本 skill 走另一条路:

- **Agent 联网调研**:自主搜索开源项目、大厂博客、技术社区、学术文档,建立证据基础
- **人**(架构师 / 解决方案工程师)必须拥有:问题定义、成功语义、核心不变量、关键取舍、失败模式、验收标准
- **AI agent** 负责:调研候选、展开分析、按 prompt 把代码、测试、压测脚本、部署配置写出来
- **harness prompt 包**就是把"人想清楚的一切"压成 agent 可执行的指令

核心理念:**AI 提供候选,人类拥有判断;再把确认后的判断压成让 AI 写代码的脚手架**。

## 工作模式

**半自动 + 3 个关键 Gate**:skill 自动推进大部分流程,只在 3 个决定架构走向的节点暂停等用户拍板:

- **Gate 1** — 业务边界 / 规模假设 / SLA / 人类判断
- **Gate 2** — 一致性策略 / 容灾等级 / 技术栈
- **Gate 3** — MVP 范围 / harness 粒度

其余决策 skill 基于调研证据和行业常识自动预填,标 `[调研:URL]` 或 `[推断]`。

## 产出结构

```
scenarios/<scenario_id>/
├── assumptions.md         # 全流程决策追踪(三个 Gate 汇总)
├── research.md            # 调研证据:开源项目、大厂方案、社区洞察
├── business.md            # 业务本质、角色、流程、关键指标
├── scale-constraints.md   # 规模估算、SLA、一致性、成本、合规
├── judgment.md            # 人类判断:问题定义/不变量/取舍/失败模式/验收
├── challenges.md          # 3–8 个核心技术难点
├── solutions.md           # 每个难点 2–3 方案对比 + 推荐
├── architecture.md        # 模块分解、数据模型、关键接口、架构图
├── playbook.md            # MVP、迭代阶段、里程碑、风险
└── harness/
    ├── master-prompt.md   # 总控(给 agent 的第一份指令)
    ├── modules/           # 模块级 prompt:职责 + 契约 + 验收
    └── tasks/             # 任务级 prompt:具体接口/算法 + DoD
```

## 如何触发

用户侧说以下话术即可触发:

- "帮我分析秒杀系统 / IM / 清结算系统…"
- "我要做一个 [场景] 的系统设计"
- "出一份可以喂给 Claude Code / Codex 的 prompt 包"
- "用 harness engineering 方式拆这个业务"

## 快速上手(两条流程)

### 流程 A:从零开始做一个场景

1. 触发 skill:"分析秒杀系统"
2. Skill 进入 Step 0,抽场景元数据,给默认假设
3. **Step R 联网调研**:自动搜索开源项目、大厂博客、社区帖 → 产出 `research.md`
4. Step 1–2 产出 `business.md`、`scale-constraints.md`,Step J 产出 `judgment.md` → **Gate 1 确认**
5. Step 3–4 产出 `challenges.md`、`solutions.md` → **Gate 2 确认**
6. Step 5–6 产出 `architecture.md`、`playbook.md` → **Gate 3 确认**
7. Step 7 产出 `harness/*`
8. Step 8 自检 + 交付
9. 把 `harness/master-prompt.md` 丢给 Claude Code / Codex 执行

### 流程 B:已有部分分析,补齐 harness

1. 把已有文档放进 `scenarios/<scenario_id>/`
2. 告诉 skill:"已有 business/challenges/architecture,请补齐 solutions + playbook + harness"
3. Skill 定位缺口,直接从对应 Step 开始,经过剩余 Gate,产出剩下的交付物

## 文件结构

```
scenario-harness-architect/
├── SKILL.md                               # 主入口:触发条件、工作流、约束、质量闸门
├── README.md                              # (本文件)
└── references/
    ├── scenario-workflow.md               # 8 步工作流 + 3 个 Gate 的交互脚本
    ├── analysis-dimensions.md             # 4 层分析维度的方法论
    ├── harness-prompt-design.md           # 分层 prompt 的设计哲学
    ├── output-doc-templates.md            # 8 份核心文档骨架模板
    ├── human-judgment-template.md          # 人类判断文档模板与判断题库
    └── harness-prompt-templates.md        # master / modules / tasks 三层 prompt 模板
```

## 与 tensei-skills 其他 skill 的关系

| Skill | 区别 |
|-------|------|
| `repo-architecture-analyst` | 输入是**已有仓库**,逆向提炼场景方案;本 skill 输入是**场景题目**,正向产出方案 + harness |
| `ddd-longform-arch-doc` | 产出单份 DDD 长文档,读者是人;本 skill 产出**分析文档 + agent 可执行 prompt** 两类产物 |
| `sdd-plane-bridge` | 把计划同步到 Plane;本 skill 聚焦"产出可被 agent 执行的 prompt",不涉及 Plane 流转(可以后续把 playbook.md 的任务同步到 Plane) |

## 适合谁

- 架构师 / 解决方案工程师:想用 AI 驱动研发,而不是手工写代码
- 面试系统设计题复盘:从场景题到完整的方案 + 可执行包
- 技术 TL:需要把一个新业务拆出可并行执行的任务清单,并让 agent 接手实现
- 对 "AI coding agent 编排" / "harness engineering" 感兴趣的工程师

## 不适合谁

- 只想改一两个接口、做单点开发 → 去用 `sdd-plane-bridge` 或直接开发
- 想要一份"系统设计答案"写给产品看 → 用 `ddd-longform-arch-doc`
- 要逆向分析已有仓库 → 用 `repo-architecture-analyst`

## 版本与维护

- 版本:v0.2.0
- 语言:中文为主(产出也以中文为主)
- 维护建议:
  - 新增场景类型时,在 `references/scenario-workflow.md` 的"常见场景默认规模速查"补一条
  - 新增 coding agent 差异时,在 `references/harness-prompt-design.md` 的"Agent 差异适配"补一节
  - 新增需要人类亲自判断的通用问题时,在 `references/human-judgment-template.md` 的"场景判断题库"补一条
  - 难点/方案库发现新的通用模式时,在 `references/analysis-dimensions.md` 的"10 条线索"或"横切方案"扩展
