# Quality Checklist

Before finalizing a technical decision point analysis, verify:

## Decision framing

- Each decision is phrased as an architecture fork, not a technology label.
- Each decision explains why it matters to the business scenario.
- P0/P1 decisions are clearly separated from implementation details.
- Business decisions disguised as technical decisions are marked `[待确认]`.

## Options and trade-offs

- Each important decision has at least two viable options.
- Each option explains mechanism, benefit, cost, and best-fit condition.
- The recommendation is explicit and tied to assumptions.
- The analysis states what breaks if the wrong option is chosen.

## Correctness and consistency

- Hard invariants are listed.
- Strong-consistency boundaries are identified.
- Eventually consistent paths include retry, idempotency, reconciliation, and observability.
- Truth sources and projections are not confused.

## Failure and recovery

- Every async boundary has an idempotency key.
- Every external side effect has duplicate prevention.
- Every retry path has a limit or dead-letter/compensation path.
- Payment, inventory, order, and fulfillment failures are not hidden behind generic errors.

## Operations

- Critical metrics are named.
- Degradation switches state what they protect and what they sacrifice.
- Acceptance checks include load, failure, and reconciliation tests.

## Output quality

- The report is concrete enough for an engineer or coding agent to act on.
- It avoids vague terms like `high availability`, `reasonable`, or `optimize` without mechanisms.
- It ends with a clear recommended architecture posture.
