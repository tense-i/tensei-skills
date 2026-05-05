# Dependency Contracts

This orchestrator skill integrates specialized skills through clear contracts. Do not require every child skill to produce the same format; normalize their output into the OS loop.

## Information Intake Skills

### hackernews-topn-explainer

Expected inputs:
- top N value, topic filter, or date if available.

Extract from output:
- top items;
- why each matters;
- technical/product/social trend;
- whether it should influence this week's theme.

Normalize to:

```markdown
## Signal
- Source: Hacker News
- Item:
- Trend:
- Related scenario:
- Judgment impact:
```

### github-trending-quick-deep-analyzer

Expected inputs:
- trending language/topic/date or specific repositories.

Extract from output:
- what problem the repository solves;
- target users;
- adoption signal;
- technical pattern;
- business or learning opportunity.

Normalize to:

```markdown
## Signal
- Source: GitHub Trending
- Repo:
- Problem solved:
- Pattern:
- Related scenario:
- Judgment impact:
```

### track-awesome-yesterday-analyzer

Expected inputs:
- usually none, or a target date.

Extract from output:
- updated awesome lists;
- newly added items;
- domain trend;
- items worth deep-diving.

Normalize to:

```markdown
## Signal
- Source: Track Awesome List
- Updated list/item:
- Domain trend:
- Related scenario:
- Judgment impact:
```

## Scenario Skill

### scenario-harness-architect

Use when the user wants deep scenario analysis, not just a daily lightweight entry.

Expected inputs:
- scenario name;
- business context if known;
- scale hints if known;
- target depth;
- target agent if harness is needed.

Extract from output:
- problem definition;
- constraints;
- human judgment items;
- core difficulties;
- solution trade-offs;
- architecture or harness prompts.

Normalize to the OS loop:

```markdown
## Scenario Judgment
- Core judgment:
- Trade-off:
- Failure mode:
- AI harness implication:
- Weekly asset contribution:
```

## Future Job-Market Skill

Recommended skill name: `boss-market-calibrator`.

Responsibilities:
- analyze job posts from BOSS or equivalent sources;
- extract high-frequency role requirements;
- infer what market pays for;
- compare with user's ability map;
- produce 30-day gap recommendations without causing broad anxiety.

Output contract:

```markdown
## Market Calibration
- Role cluster:
- Salary band:
- Repeated requirements:
- Business scenario behind the role:
- Market-paid ability:
- User's current match:
- One gap worth improving:
- One gap to ignore for now:
```

## Future Social-Practice Skill

Recommended skill name: `social-practice-coach`.

Responsibilities:
- analyze workplace and interpersonal situations;
- distinguish positions, interests, risks, and hidden constraints;
- produce mature wording;
- design one small behavioral experiment;
- review the result later.

Output contract:

```markdown
## Social Practice
- Scenario:
- Stakeholders:
- Hidden interests:
- Bad default reaction:
- Better response:
- One experiment:
- Review question:
```

## Integration Rule

After any dependency returns output, compress it into exactly one of these slots:

1. Signal.
2. Scenario judgment.
3. Language expression.
4. Market calibration.
5. Social practice.
6. Asset contribution.

If an output does not affect at least one slot, it is probably noise and should be omitted.
