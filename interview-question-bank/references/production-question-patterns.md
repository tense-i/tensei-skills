# Production Question Patterns

## Pattern A: State transition failure
Ask what happens when a transition succeeds halfway.

Template:
[Action A] succeeds but [Action B] fails. What state is left behind? Who detects it? Who repairs it?

Examples:
- Redis 扣库存成功但创建订单失败怎么办？
- 支付成功但普通订单同步失败怎么办？
- 发卡成功但订单状态更新失败怎么办？

## Pattern B: Fact source ambiguity
Ask who owns truth.

Template:
[Cache/MQ/view] says X, but [DB/payment/domain] says Y. Which one is truth? How do you converge?

Examples:
- Redis 库存和数据库库存不一致怎么办？
- 前端看到排队中，但订单表已有订单怎么办？

## Pattern C: Timing boundary
Ask what happens before/after business window.

Examples:
- 活动还没开始就有请求进来怎么办？
- 活动结束后队列里还有消息怎么办？
- 支付超时消息和支付成功回调并发怎么办？

## Pattern D: Atomicity boundary
Ask what must be in the same atomic operation.

Examples:
- Lua 里哪些校验和扣减必须一起做？
- 限购计数和库存扣减拆开会产生什么竞态？

## Pattern E: Operational proof
Ask how to prove the system worked.

Examples:
- 如何证明没有超卖？
- 如何证明支付成功订单都最终发卡？
- 压测成功标准是什么？
