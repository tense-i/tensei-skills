# 分层 Harness Prompt 模板(master / modules / tasks)

> 本文档提供三层 prompt 的**可填骨架**。
> 设计哲学与质量闸门见 `harness-prompt-design.md`。
> 使用方式:把 `{...}` 替换为当前场景的具体内容,保留锚点与章节。

---

## 文件组织与命名

```
harness/
├── master-prompt.md
├── modules/
│   ├── M01-<slug>.md          # 例:M01-seckill-order.md
│   ├── M02-<slug>.md
│   └── ...
└── tasks/
    ├── M01-T01-<slug>.md      # 例:M01-T01-create-order-api.md
    ├── M01-T02-<slug>.md
    └── ...
```

**命名原则**:
- `M{编号}` 两位数字,保留排序余地(M01..M99)
- `T{编号}` 两位数字,任务在模块内排序(T01..T99)
- `<slug>` 用 kebab-case 业务语义,不用技术词

---

## 1. master-prompt.md 模板

```markdown
# {Scenario 名称} — Master Prompt(总控指令)

> 读者:你(coding agent,如 Claude Code / Codex / Cursor)
> 作用:本项目的第一份指令。读完它你就知道:做什么、哪些判断已经由人类确认、怎么做、按什么顺序做、做到什么程度算完。
> 版本:v{N}(对应 assumptions.md Gate 3 的决策版本)

---

## 0. 执行协议

请你按以下协议工作:

1. 先通读本文档全部内容,建立全局理解
2. 再读取 `../judgment.md`,确认成功语义、核心不变量、关键取舍、失败模式和验收标准
3. 再按 §9 给出的**模块执行顺序**,逐个读 `modules/M{N}-*.md`
3. 每进入一个模块前,先读其 task 索引,再按顺序读 `tasks/M{N}-T{M}-*.md`
4. 每完成一个 task,运行其 DoD 中定义的测试确保通过
5. 每完成一个模块,运行模块验收脚本
6. 所有模块完成后,运行全局质量门(§10)
7. 交付前输出产物清单

**若遇到信息不够或决策冲突**,停下来找我(架构师),不要擅自决定。

---

## 1. 项目定位

> {一句话:这个系统做什么,为谁做}

详见 `../business.md`。

## 2. 人类已确认判断(不可擅自改变)

> 以下内容来自 `../judgment.md`。你可以实现、测试、补充边界,但不能擅自改变这些判断。若发现冲突,停止执行并写入 `reports/questions.md`。

### 2.1 成功语义

{粘贴 judgment.md §1.2 的成功语义表摘要}

### 2.2 核心不变量

| 编号 | 不变量 | 证明方式 |
|---|---|---|
| I1 | ... | ... |

### 2.3 关键取舍与代价

| 决策 | 已确认选择 | 放弃 | 代价 |
|---|---|---|---|
| ... | ... | ... | ... |

### 2.4 不可接受失败模式

| 失败场景 | 必须行为 | 验收测试 |
|---|---|---|
| ... | ... | ... |

## 3. 硬约束(不可违反)

### 2.1 规模与 SLA

- 峰值 QPS:{数字}
- 核心路径 P99:≤ {X} ms
- 可用性:{99.95% / 99.99%}
- 一致性:{关键路径强一致,其余最终一致,详见 `../scale-constraints.md` §4}

### 2.2 技术栈(Gate 2 已确认)

| 分类 | 选型 | 版本 |
|------|------|------|
| 语言 | Go | 1.22 |
| Web 框架 | Gin | v1.9+ |
| RPC | gRPC | - |
| 关系库 | PostgreSQL | 15 |
| 缓存 | Redis | 7 |
| 消息 | Kafka | 3.x |
| 部署 | Kubernetes | - |
| 观测 | Prometheus + Loki + Jaeger | - |

### 2.3 代码与工程规范

- 代码风格:gofmt + golangci-lint(规则见 `.golangci.yml`)
- 提交规范:Conventional Commits
- 分支模型:trunk-based(main 直推,feature 短分支)
- 代码审查:每个 PR 必须有测试 + 文档

### 2.4 安全与合规

- {引用 scale-constraints §7}
- 所有用户输入必须校验
- 敏感数据(身份证/手机号/支付信息)必须加密或脱敏
- 日志不允许打印敏感字段

## 4. 参考实现(来自调研)

> 以下开源项目已被验证可解决本场景的核心难点,供你参考架构思路和实现细节。**不要盲目照搬**,按本项目的约束做取舍。

| # | 项目名 | 关联难点 | 可参考点 | 不适用点 | URL |
|---|--------|---------|---------|---------|-----|
| 1 | {project1} | C1, C3 | {具体架构思路} | {规模/语言/场景差异} | {url} |
| 2 | {project2} | C2 | {具体做法} | {差异点} | {url} |

详见 `../research.md` §1（开源项目速查）。

## 5. 整体架构(快照)

{粘贴 architecture.md §1 的系统架构图}

详细架构、时序、数据模型见 `../architecture.md`。

## 6. 目录规约(请按此结构创建)

```
{project-root}/
├── cmd/
│   └── {app-name}/main.go
├── internal/
│   ├── m01-{slug}/           # 模块 M01
│   │   ├── handler/
│   │   ├── service/
│   │   ├── repo/
│   │   └── domain/
│   ├── m02-{slug}/           # 模块 M02
│   └── shared/               # 共享组件(中间件、工具、错误码)
├── api/
│   └── openapi.yaml          # 对外 API 契约
├── migrations/               # DB 迁移
├── bench/                    # 压测脚本
├── deploy/
│   ├── k8s/
│   └── docker/
├── test/
│   ├── e2e/
│   └── integration/
├── docs/
└── Makefile
```

## 7. 核心数据模型(快照)

{粘贴 architecture.md §4 的关键表/Key 定义}

详见 `../architecture.md` §4。

## 8. 关键业务流程(快照)

{粘贴 architecture.md §2 的主路径时序图}

详见 `../architecture.md` §2。

## 9. 模块清单与执行顺序

按以下**拓扑顺序**执行(被依赖的在前):

| 顺序 | 编号 | 模块名 | 对应难点 | prompt |
|------|------|-------|---------|--------|
| 1 | M01 | {共享基础:错误码 + 中间件 + 观测} | — | `modules/M01-shared-foundation.md` |
| 2 | M02 | {模块名} | C1 | `modules/M02-{slug}.md` |
| 3 | M03 | {模块名} | C2, C3 | `modules/M03-{slug}.md` |
| ... | ... | ... | ... | ... |

**执行规则**:
- 模块之间只能用**契约**(接口/事件)通信,不能直接引用内部实现
- 模块内部自由,模块外部严格按接口契约
- 完成一个模块 → 跑通该模块的集成测试 → 才能进入下一个

## 10. 全局质量门(项目完成标准)

| 项 | 标准 | 如何验证 |
|----|------|---------|
| 单元测试 | 覆盖率 ≥ 80% | `go test -cover ./...` |
| 集成测试 | 核心主路径 100% 覆盖 | `make test-integration` |
| 压测 | P99 ≤ {X}ms @ {Y} QPS 稳定 5 min | `make bench` |
| Lint | 零警告 | `make lint` |
| 安全 | 无 High+ 漏洞 | `make sec-scan` |
| 文档 | 每个模块有 README | `docs/` 下完整 |
| 容灾 | 故障注入演练通过 | `make chaos-test` |
| 可观测 | 每个关键路径有 metric + log + trace | Grafana dashboard 截图 |

## 11. 交付物

完成全部模块后,在 `reports/delivery.md` 输出:

1. 已完成模块清单 + 每个模块测试结果
2. 压测报告(含图表)
3. 已知风险与限制(诚实,不藏雷)
4. 下一阶段建议

## 12. 失败与求助协议

如果:
- 某个 task 的实现要点你认为会违反 `judgment.md` 中的人类已确认判断
- 某个 task 的实现要点你认为会违反硬约束
- 某个 module 的契约存在模糊之处
- 某个 DoD 无法在当前环境验证

**停止执行**,在 `reports/questions.md` 记录问题并等待架构师回复。不要自己凭"看起来合理"做决策。

---

## 13. 启动指令

开始吧。第一步:读 `modules/M01-shared-foundation.md`,按其指令创建共享基础层。
```

---

## 2. modules/M{N}-<slug>.md 模板

```markdown
# M{N} — {模块名称}

> 读者:coding agent
> 所属项目:{Scenario 名称}
> 上游 master-prompt:`../master-prompt.md`

---

## 1. 职责(一句话)

> {这个模块做什么,不做什么}

### 1.1 核心能力(本模块必须实现)

- 能力 1:...
- 能力 2:...
- 能力 3:...

### 1.2 边界(本模块不做)

- 不做 X(由 M0? 负责)
- 不做 Y

## 2. 对应难点与人类判断

- {C{n}:引用 `../../challenges.md` §C{n}}
- 推荐方案:{引用 `../../solutions.md` §C{n}/方案B}
- 相关人类判断:{引用 `../../judgment.md` §2/§3/§4 中的不变量、取舍、失败模式}

## 3. 架构位置

```mermaid
graph LR
    U[上游:{调用方}] --> THIS[M{N}]
    THIS --> D1[下游:{依赖1}]
    THIS --> D2[下游:{依赖2}]
    THIS -.事件.-> MQ[Kafka.{topic}]
```

## 4. 依赖关系

### 4.1 上游依赖(被谁调用)

| 调用方 | 接口 | 协议 |
|--------|------|------|
| Gateway | CreateXXX | HTTP |
| M0? | DoYYY | gRPC |

### 4.2 下游依赖(调用谁)

| 被调方 | 接口 | 协议 | 降级策略 |
|--------|------|------|---------|
| M0? | StockDeduct | gRPC | 熔断 + fail-fast |
| Redis | 缓存 | - | 穿透到 DB |
| Kafka | 事件发送 | - | 本地消息表 + 补偿 |

## 5. 对外接口契约

### 5.1 接口 1:{接口名}

- **协议**:HTTP POST
- **路径**:/v1/{path}
- **幂等**:是,幂等键 `idempotencyKey`
- **限流**:100 QPS / 用户
- **鉴权**:JWT

**请求**:

```json
{
  "userId": 123,
  "productId": 456,
  "quantity": 1,
  "idempotencyKey": "uuid-v4"
}
```

**响应(成功)**:

```json
{
  "orderId": "...",
  "status": "pending_payment"
}
```

**错误码**:

| 码 | HTTP | 语义 | 可重试 |
|----|------|------|-------|
| INSUFFICIENT_STOCK | 409 | 库存不足 | 否 |
| DUPLICATE | 200 | 重复请求返回原结果 | 是(幂等) |
| INVALID_PARAM | 400 | 参数错误 | 否 |
| RATE_LIMITED | 429 | 限流 | 是(退避) |

### 5.2 接口 2:{接口名}

(同上结构)

## 6. 内部组件拆分

```
m{N}-{slug}/
├── handler/    # HTTP/gRPC 入口
├── service/    # 业务编排
├── domain/     # 领域模型 + 状态机
├── repo/       # 持久化
└── event/      # 事件发送/消费
```

## 7. 数据归属

本模块拥有以下数据:

| 实体 | 存储 | 访问规则 |
|------|------|---------|
| order | PostgreSQL.orders | 本模块独占写;其他模块只读(通过 API 或 CDC) |
| stock_cache | Redis.stock:{actId}:{skuId} | 本模块独占 |
| order.created 事件 | Kafka.order.created | 本模块发送 |

## 8. 技术选型提示

- 状态机:用 `github.com/looplab/fsm` 管理订单状态流转
- 幂等:用 `singleflight` + 幂等表(DB)组合
- 分布式锁:用 Redlock(go-redsync/redsync)
- 日志:zap,必带 trace_id

## 9. 任务清单

按顺序执行:

| 顺序 | 编号 | 任务名 | prompt |
|------|------|-------|--------|
| 1 | T01 | 定义领域模型与状态机 | `../tasks/M{N}-T01-domain-model.md` |
| 2 | T02 | 实现数据访问层 | `../tasks/M{N}-T02-repo.md` |
| 3 | T03 | 实现库存预扣 | `../tasks/M{N}-T03-stock-deduct.md` |
| 4 | T04 | 实现下单编排 | `../tasks/M{N}-T04-create-order.md` |
| 5 | T05 | 接入 HTTP 入口 | `../tasks/M{N}-T05-http-handler.md` |
| 6 | T06 | 事件发送 | `../tasks/M{N}-T06-event-publish.md` |
| 7 | T07 | 集成测试 | `../tasks/M{N}-T07-integration-test.md` |

## 10. 模块验收标准

> 验收必须能证明 `../../judgment.md` 中与本模块相关的不变量成立,不能只证明接口能跑。

### 10.1 功能验收

- [ ] 所有对外接口按契约实现
- [ ] 所有错误码在对应路径上能触发
- [ ] 幂等:同一 idempotencyKey 调用多次返回一致

### 10.2 性能验收

- [ ] P99 ≤ {X} ms @ {Y} QPS
- [ ] 压测脚本:`bench/m{N}-{slug}.js`
- [ ] 压测报告:5 分钟稳定无错误

### 10.3 一致性验收

- [ ] 并发下单抢 10 个库存,恰好 10 个成功(无超卖)
- [ ] 下单失败时库存预扣已回滚
- [ ] 订单与事件的一致性(事件必发或补偿)

### 10.4 可观测性验收

- [ ] 关键路径有 metric(RED:Rate/Errors/Duration)
- [ ] 关键路径有 trace(跨服务串起来)
- [ ] 关键路径有结构化日志(含 trace_id)

## 11. 完成信号

当上述 10.1 ~ 10.4 全部勾掉,在 `reports/m{N}-done.md` 输出完成报告,含:
- 任务完成清单
- 测试/压测结果截图或日志
- 已知限制与后续 TODO
```

---

## 3. tasks/M{N}-T{M}-<slug>.md 模板

```markdown
# M{N}-T{M} — {任务名称}

> 读者:coding agent
> 所属模块:M{N}({模块名}),见 `../modules/M{N}-{slug}.md`

---

## 1. 任务目标(一句话)

> {这个 task 要产出什么可运行/可测的东西}

## 2. 前置条件

在开始本任务前,以下必须就绪:

- [ ] M{N}-T{prev} 已完成,产物路径:`internal/m{N}-{slug}/domain/...`
- [ ] DB 迁移 `migrations/00X_create_xxx.sql` 已执行
- [ ] Redis / Kafka 本地或 docker-compose 已启动

**若前置未满足**,先去执行对应 task,不要凭空发挥。

## 3. 输入契约

**来源**:{谁会调用本任务产出的函数/接口}

**参数 schema**:

| 字段 | 类型 | 约束 | 来源 |
|------|------|------|------|
| userId | int64 | > 0 | 请求头解析 |
| productId | int64 | 存在于活动商品表 | 请求体 |
| quantity | int32 | 1..100 | 请求体 |
| idempotencyKey | string | UUID v4 | 请求头 `X-Idempotency-Key` |

## 4. 输出契约

### 4.1 成功返回

```go
type CreateOrderResult struct {
    OrderID string
    Status  string // "pending_payment"
}
```

### 4.2 失败返回

- 错误类型 `INSUFFICIENT_STOCK`:库存不足,不可重试
- 错误类型 `DUPLICATE`:重复请求,返回原结果(幂等语义)
- 错误类型 `INVALID_PARAM`:参数校验失败
- 错误类型 `INTERNAL`:包装原始错误,记录日志

### 4.3 副作用

| 副作用 | 对象 | 条件 |
|-------|------|------|
| DB 写入 | orders 表 insert | 成功时 |
| Redis 写入 | stock:{actId}:{skuId} 扣减 | 预扣成功时 |
| 幂等表写入 | idempotency 表 insert | 所有请求 |
| 事件发送 | Kafka `order.created` | 订单成功时 |

**所有副作用必须在失败路径上正确回滚或补偿**。

## 5. 人类判断约束

> 以下来自 `../../judgment.md`,不得擅自修改。若实现时发现冲突,停止并记录到 `reports/questions.md`。

- 相关不变量:{I?}
- 相关取舍:{D?}
- 相关失败模式:{F?}
- 相关验收标准:{V?}

## 6. 实现要点

> **这里给方向,不给完整代码。agent 自己实现,但必须符合这些约束**:

1. **幂等检查**:先查 idempotency 表;命中则直接返回原结果(不重新执行任何副作用)
2. **参数校验**:quantity 和 productId 的合法性,参数错直接返回 INVALID_PARAM
3. **活动校验**:商品在活动时段内、未下架、当前用户有参与资格
4. **库存预扣**:调用 M02 的 `DeductStock(actId, skuId, qty, orderCtx)`,失败立即返回 INSUFFICIENT_STOCK
5. **DB 事务**:事务内 insert 订单主表 + 订单项表 + 幂等表;失败必须回滚库存预扣
6. **事件发送**:DB 事务外通过本地消息表发送 `order.created`(保证至少一次)

**关键取舍**(已在 solutions.md §C1 决定):
- 库存用 Redis Lua 原子预扣,而非 DB 悲观锁
- 事件发送用本地消息表 + 异步 relay,而非事务消息

## 7. 边界与异常

| 场景 | 行为 |
|------|------|
| 重复 idempotencyKey | 返回首次结果,不执行副作用 |
| 库存恰好为 0 | 返回 INSUFFICIENT_STOCK,不扣 |
| 库存预扣成功但 DB 写入失败 | 回滚 Redis 预扣(调 M02 的 `RestoreStock`),返回 INTERNAL |
| 本地消息表写入失败 | 整体回滚,返回 INTERNAL |
| 超时(3s) | 返回 INTERNAL + 异步补偿清理预扣 |
| 并发:同用户同 key 重复请求 | singleflight 合并,只执行一次 |

## 8. 幂等性约束

- **幂等键**:`idempotencyKey`(UUID v4,客户端生成)
- **保留期**:24 小时
- **实现**:幂等表 `idempotency(key, result_snapshot, created_at)`,Redis 作为快速路径
- **验证**:同一 key 调用 100 次,DB 订单数恰好 1 条,库存只扣 1 次

## 9. Definition of Done

1. 函数/接口按契约实现
2. 单元测试覆盖以下用例(必须全过):
   - [ ] 正常下单:1 条
   - [ ] 参数错误(quantity=0):1 条
   - [ ] 库存不足:1 条
   - [ ] 重复 idempotencyKey:返回原结果,副作用只发生 1 次
   - [ ] 预扣成功但 DB 失败:Redis 预扣被正确回滚
   - [ ] 并发 100 协程抢 10 个库存:恰好 10 个成功,其余 INSUFFICIENT_STOCK
   - [ ] 依赖 M02 超时:返回 INTERNAL + 异步补偿启动
3. 压测脚本 `bench/m{N}-t{M}.js`:2000 QPS 稳定 1 分钟,P99 ≤ {X}ms
4. 覆盖率 ≥ 80%
5. lint 通过,无 TODO
6. 有至少 1 个 metric(`m{N}_t{M}_requests_total` + `m{N}_t{M}_duration_seconds`)
7. 有 1 条结构化日志(成功 info,失败 error,必带 trace_id)

## 10. 最小测试清单

```go
func TestCreateOrder_Normal(t *testing.T) {
    // 准备:活动、库存 10、用户
    // 调用:quantity=1
    // 断言:返回 orderId + status=pending_payment;DB 有订单;Redis 库存=9;事件入本地消息表
}

func TestCreateOrder_Idempotent(t *testing.T) {
    // 准备:同上
    // 调用:同一 idempotencyKey 连续 3 次
    // 断言:3 次返回相同 orderId;DB 订单数=1;Redis 扣减 1 次;事件 1 条
}

func TestCreateOrder_ConcurrentOversell(t *testing.T) {
    // 准备:库存=10
    // 调用:100 协程并发 quantity=1
    // 断言:成功数=10;INSUFFICIENT_STOCK=90;Redis 库存=0;DB 订单数=10
}

// ...其余用例
```

## 11. 完成信号

实现完毕后,在 `reports/m{N}-t{M}-done.md` 输出:
- 对应 `judgment.md` 不变量的验证结果
- 测试结果截图 / 日志
- 压测 P99 与 QPS 实测值
- 已知限制

如果有任何实现要点与契约冲突,或发现 Gate 决策遗漏的边界,**停下来,在 `reports/questions.md` 记录**。
```

---

## 4. 简化变体:轻量 task(当粒度为"粗"时使用)

当 Gate 3 选择**粗粒度**(2–3 task/模块)时,每个 task 覆盖面更大,可用以下简化结构:

```markdown
# M{N}-T{M} — {大任务名}

## 1. 任务目标
> {一段话描述这个大任务完成什么功能}

## 2. 子能力清单
- 子能力 A(含输入/输出/副作用)
- 子能力 B(同上)
- 子能力 C(同上)

## 3. 整体输入/输出契约
(合并的契约)

## 4. 实现要点
(整体指导)

## 5. Definition of Done
(整合的 DoD)

## 6. 测试清单
(每个子能力至少 2 条测试)
```

**注意**:粗粒度对 agent 要求高,建议只用于**简单模块**(如纯 CRUD 或纯工具函数)。核心业务模块必须用细粒度。

---

## 5. Prompt 之间的引用规范

为避免重复且保证一致性,统一采用以下引用写法:

| 场景 | 写法 |
|------|------|
| 引用分析文档某节 | `详见 ../../solutions.md §C3/方案B` |
| 引用上游 master | `全局约束见 ../master-prompt.md §2` |
| 引用同级模块 | `接口契约见 ../modules/M02-stock.md §5.1` |
| 引用前置 task | `前置依赖 tasks/M{N}-T{prev}.md,其产物:...` |

**不允许**:
- 直接复述大段背景(用引用)
- 引用路径相对错位(master 引用时从 harness/ 起算,module 从 modules/ 起算,task 从 tasks/ 起算)

---

## 6. 常用片段库

### 6.1 幂等性说明标准片段

```markdown
### 幂等性

- 幂等键:`{key}`,由 {客户端生成 / 业务唯一键 / ...}
- 保留期:{24h / 7d / ...}
- 实现位置:{幂等表 + Redis 快速路径}
- 验证:同一 key 重复 N 次,关键副作用只发生 1 次
```

### 6.2 错误码表标准片段

```markdown
### 错误码

| 码 | HTTP | 语义 | 可重试 | 客户端建议 |
|----|------|------|-------|-----------|
| ... | ... | ... | 是/否 | ... |
```

### 6.3 压测要求标准片段

```markdown
### 压测

- 脚本:`bench/{path}.js`(k6)
- 场景:{描述流量形态:持续 / 阶梯 / 尖峰}
- 目标:{Y} QPS 稳定 N 分钟
- 验收:P99 ≤ {X} ms,错误率 ≤ 0.1%
- 产出:报告 `reports/bench-{path}.md` + Grafana 截图
```

### 6.4 可观测性标准片段

```markdown
### 可观测性

**Metrics**(Prometheus):
- `{ns}_{name}_requests_total{status}` — 请求计数
- `{ns}_{name}_duration_seconds{quantile}` — 延迟分布
- `{ns}_{name}_inflight` — 正在处理的请求

**Logs**(zap,结构化):
- 每条日志必带 `trace_id`, `user_id`, `request_id`
- 敏感字段(手机号/身份证/支付信息)必须脱敏
- 级别:正常 info,异常 error,调试 debug

**Traces**(OpenTelemetry):
- span 覆盖:入口 → 业务编排 → 数据访问 → 外部调用
- 属性:业务关键字段(order_id, user_id)
```

用这些片段可以让多个 task 的观测/压测标准保持一致,减少 agent 随意发挥。
