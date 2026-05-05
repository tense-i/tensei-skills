# Technical Decision Point Taxonomy

Use this taxonomy to discover architecture decisions in a business system.

## 1. Business semantics decisions

Ask:
- What does success mean to the user?
- Which intermediate states are visible?
- Which failures are acceptable, delayed, or unacceptable?
- Which business promises cannot be broken?

Examples:
- `入队成功` vs `抢购成功` vs `支付成功` vs `发卡成功`.
- Whether unpaid released inventory can re-enter the sale.
- Whether delayed fulfillment is acceptable after payment.

## 2. Traffic and admission decisions

Ask:
- Where is peak traffic admitted?
- What is rejected at the edge?
- What traffic is allowed into queues?
- Which dimensions need independent limits?

Common options:
- Gateway/global rate limiting.
- User/IP/device/activity/SKU rate limiting.
- Admission tokens.
- Sold-out markers.
- Queue length protection.

## 3. Sync vs async boundary decisions

Ask:
- What must finish before returning to the user?
- Where does the system switch to MQ/event/job processing?
- What state does the user see during async processing?

Common boundaries:
- Before inventory arbitration.
- After inventory freeze.
- After flash-sale order creation.
- After payment success.
- Before fulfillment.

## 4. Scarce-resource arbitration decisions

Ask:
- Who decides the winner?
- Is the decision made at request time, queue-consumption time, or payment time?
- Does the decision freeze inventory, deduct inventory, or only admit to a queue?

Common options:
- DB conditional update.
- Redis Lua.
- Queue ordering.
- Pre-generated tokens.
- Inventory segmentation.

## 5. Truth-source decisions

Ask:
- Which system owns the durable fact?
- Which systems only hold projections or caches?
- Can the projection be rebuilt?

Common truth sources:
- Payment system for payment facts.
- Order DB for durable order facts.
- Inventory ledger for inventory facts.
- Account/entitlement system for benefits or balances.
- Redis for transient arbitration or state projection, not durable truth.

## 6. Consistency decisions

Ask:
- What must be strongly consistent?
- What can be eventually consistent?
- What proves convergence?

Strong consistency candidates:
- No over-sell.
- No duplicate payment handling.
- No duplicate entitlement issuance.
- No duplicate order creation.

Eventually consistent candidates:
- User-facing list refresh.
- Statistics dashboards.
- Cross-domain order synchronization.
- Fulfillment status display.

## 7. Idempotency decisions

Ask:
- What operation can be repeated?
- What idempotency key prevents duplicate side effects?
- Is Redis enough, or is a DB unique key required?

Common keys:
- Participation: `userId + activityId + skuId`.
- Queue message: `traceId`.
- Order: `orderNo` or `userId + activityId + skuId`.
- Payment callback: `outTradeNo + transactionNo`.
- Fulfillment: `orderNo + benefitType`.

## 8. Queue and partitioning decisions

Ask:
- What resource becomes hot?
- Should queues be partitioned by activity, SKU, product, user hash, or shard?
- How are hot partitions isolated or degraded?

Common trade-off:
- One queue is simple but vulnerable to head-of-line blocking.
- SKU queues isolate product hotspots but increase routing/ops complexity.
- User-hash queues distribute load but may not isolate inventory hotspots.

## 9. Data model and sharding decisions

Ask:
- What is the dominant read path?
- What is the dominant write path?
- Which key avoids hot partitions?
- Can master/detail records be co-located?

Common keys:
- `userId` for user order list.
- `orderNo` with embedded shard id for point lookup and co-location.
- Avoid using only `activityId` for high-write flash-sale orders because the activity itself is hot.

## 10. State machine decisions

Ask:
- What states exist?
- Which transitions are legal?
- Which states are terminal?
- Which failures create compensating states rather than rolling back facts?

Example states:
- `waiting`, `processing`, `failed_no_stock`, `wait_pay`, `paid`, `syncing`, `synced`, `fulfilling`, `finished`, `canceled`, `compensating`.

## 11. Recovery and compensation decisions

Ask:
- How is failure detected?
- Is retry safe?
- What happens after retry exhaustion?
- What reconciliation job proves convergence?
- What can humans repair manually?

Common mechanisms:
- Delay queue.
- Periodic scanner.
- Dead-letter queue.
- Outbox table.
- Compensation table.
- Reconciliation reports.
- Manual repair console.

## 12. Observability and runbook decisions

Ask:
- What metrics reveal the system is failing?
- Which metric triggers closing an entry point?
- Which dashboard proves no over-sell or lost paid order?

Typical metrics:
- Entry QPS, rejection rate, token failure rate.
- Redis Lua latency, hot keys, stock success/failure counts.
- MQ enqueue TPS, consume TPS, lag, dead letters.
- DB write latency, connection pool usage, shard skew.
- Payment callback delay and duplicate callbacks.
- Reconciliation backlog and compensation success rate.
