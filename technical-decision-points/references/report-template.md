# Technical Decision Points Report Template

Use this as the default output structure.

# [Scenario] technical decision points

## 0. Executive summary

- One-sentence system framing.
- Top 3-5 decisions that shape the architecture.
- Recommended architecture posture.

## 1. Scenario framing

| Item | Description |
|---|---|
| Business goal | ... |
| Core flow | ... |
| Critical resources | ... |
| Scale assumptions | ... |
| Hard invariants | ... |
| Acceptable delays | ... |

## 2. Decision map

Group decisions by layer:

```text
entry/admission
queue/async
resource arbitration
order/payment/fulfillment
data/truth source
recovery/operations
```

## 3. Ranked decision list

| ID | Decision | Priority | Why it matters | Recommended posture |
|---|---|---|---|---|
| D1 | ... | P0 | ... | ... |

## 4. Deep decision cards

Repeat for each P0/P1 decision:

```markdown
## D{n}. [decision name]

### What is being decided?

### Why it matters

### Options
| Option | Mechanism | Strengths | Costs / risks | Best fit |
|---|---|---|---|---|

### Failure modes if chosen poorly

### Recommended choice

### Invariants to protect

### Validation / acceptance checks
```

## 5. Cross-decision interactions

Explain how choices constrain each other. Examples:

- If the entry service pre-deducts inventory, MQ-send reliability and inventory compensation become P0.
- If the queue arbitrates winners, Redis state projection and user waiting UX become P1.
- If payment success is a strong fact, order-sync failure must be compensated rather than rolled back.

## 6. Recommended architecture posture

State the final posture in one compact paragraph and one flow diagram or text chain.

## 7. Failure modes and runbook implications

| Failure | Detection | Immediate action | Recovery path | Owner |
|---|---|---|---|---|

## 8. Implementation handoff / prompt harness

When useful, provide module-level instructions:

- Module responsibilities.
- State machines.
- Idempotency keys.
- Required tests.
- Forbidden shortcuts.
