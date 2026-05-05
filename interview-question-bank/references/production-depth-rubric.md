# Production Depth Rubric

Use this rubric to upgrade shallow interview questions into production-grade questions.

## Five levels of question depth

### Level 1: Component recognition
Example: Redis 在秒杀里有什么作用？
Weak because it tests vocabulary only.

### Level 2: Mechanism explanation
Example: Redis Lua 为什么能防止并发扣库存时超卖？
Tests basic mechanism.

### Level 3: Boundary and failure mode
Example: Lua 扣库存成功后订单创建失败，库存、限购计数、用户占位如何恢复？
Tests real engineering thinking.

### Level 4: Trade-off and decision
Example: 先预扣再发 MQ 还是先发 MQ 再预扣？在 100 万请求抢 1 万库存时，你如何选择？
Tests architecture judgment.

### Level 5: Proof and operation
Example: 活动后发现 Redis 库存和 DB 库存不一致，你如何用订单、支付、库存流水、补偿记录证明系统最终收敛？
Tests production ownership.

## Upgrade rules

For every shallow topic, add:
1. concrete scenario
2. concurrency/failure condition
3. state transition affected
4. consistency requirement
5. recovery mechanism
6. metric or proof

## Example upgrades

Shallow: MQ 有什么作用？
Deep: 库存已经售罄但 MQ 里还有 30 万条待消费消息，消费者应该逐条失败、批量丢弃、还是关闭队列？如何避免误杀支付中订单？

Shallow: 如何防重复下单？
Deep: 用户连续点击、HTTP 重试、MQ 重复投递、支付重复回调同时发生时，你的幂等键分别是什么？Redis 判重和 DB 唯一索引如何分工？
