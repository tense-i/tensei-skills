---
name: personal-judgment-os
description: orchestrate a personal ai-era growth operating system for daily judgment training, weekly theme planning, information intake, scenario analysis, language output, market calibration, and social practice. use when the user asks to run, design, review, or maintain a daily mustdo system; build a personal ability operating system; plan weekly/monthly learning themes; coordinate hacker news, github trending, track awesome list, scenario harness, boss/job-market review, or workplace/social-practice reflection; or convert scattered self-improvement tasks into a closed-loop training workflow.
---

# Personal Judgment OS

## Purpose

Run a personal ability operating system centered on **scenario judgment** rather than generic self-discipline. Use this skill to help the user complete, plan, review, or improve a recurring workflow that connects:

`signal intake -> scenario judgment -> language expression -> market calibration -> social practice -> weekly/monthly asset`

This is a **total-control/orchestrator skill**. It should not replace deeper domain skills. It should route work to existing specialized skills when the task clearly belongs to them, then integrate their outputs into one coherent personal growth loop.

## Core Principle

Do not treat the five modules as equal daily checklist items. Always organize them around one weekly theme and one daily scenario.

The main line is:

1. Build scenario judgment.
2. Use information intake as raw material.
3. Use language as expression of judgment.
4. Use market review as value calibration.
5. Use social practice as real-world validation.
6. Convert weekly work into an asset.

## Skill Composition

When available, use these specialized skills before producing the integrated result:

| Module | Preferred skill | Use for |
|---|---|---|
| Hacker News signal intake | `hackernews-topn-explainer` | Explain top HN items and extract important signals. |
| GitHub trending signal intake | `github-trending-quick-deep-analyzer` | Analyze trending repositories and their problems, users, adoption signals, and opportunities. |
| Awesome list updates | `track-awesome-yesterday-analyzer` | Analyze yesterday's Track Awesome List updates. |
| Scenario judgment / architecture / harness | `scenario-harness-architect` | Deep scenario analysis, human judgment document, architecture decisions, and agent harness. |
| Job-market calibration | future `boss-market-calibrator` or equivalent | Analyze BOSS/job posts, recurring market signals, value gaps, and role positioning. |
| Social practice | future `social-practice-coach` or equivalent | Analyze workplace/interpersonal scenarios and design small behavioral experiments. |

If a referenced skill is unavailable, continue manually using the templates in `references/templates.md` and mark the missing skill as `[dependency-missing]` in the output.

## Operating Modes

Determine the requested mode first.

### Mode A: Daily Run

Use when the user asks to run today's mustdo, daily training, daily judgment loop, or gives links/items for today's analysis.

Output a compact daily report:

1. Today's theme and scenario.
2. Signal intake summary.
3. Scenario judgment summary.
4. English/Japanese expression.
5. Market calibration.
6. Social practice insight or action.
7. One next action.

Default time budget: 60 minutes.

### Mode B: Weekly Planning

Use when the user asks to plan the week or choose themes.

Output:

1. Weekly theme.
2. Seven daily scenarios.
3. Which information skill to run each day.
4. Market/job angle for the week.
5. One social-practice experiment.
6. Expected weekly asset.

Default weekly structure:

- Monday: basic scenario.
- Tuesday: core workflow.
- Wednesday: core difficulty.
- Thursday: solution trade-offs.
- Friday: market calibration.
- Saturday: deep output.
- Sunday: review and asset consolidation.

### Mode C: Weekly Review

Use when the user asks to review a week.

Output:

1. Five key judgments.
2. Most valuable scenario.
3. Market signals.
4. Language progress.
5. Social experiment result.
6. Asset produced or missing.
7. Next week's theme.

### Mode D: Monthly Operating Review

Use when the user asks to review the month.

Output:

1. Completed themes.
2. Monthly asset.
3. Ten important judgments.
4. Market position update.
5. Language capability update.
6. Social capability update.
7. One next-month main attack direction.

### Mode E: System Design / Refactor

Use when the user asks to modify the operating system itself.

Output:

1. Diagnosis of current failure modes.
2. Proposed rule changes.
3. Updated cadence.
4. Updated templates or dependency map.
5. Risks and anti-cheat rules.

## Routing Rules

1. **If the request is about new information intake**, invoke or recommend the relevant intake skill first, then extract no more than three signals.
2. **If the request is about a specific scenario**, use `scenario-harness-architect` for deep analysis unless the user explicitly wants only a short daily entry.
3. **If the request is about jobs/BOSS/market value**, use the job-market calibration module. If the dedicated skill is missing, use the manual job template.
4. **If the request is about workplace, relationships, communication, or social practice**, use the social practice module. If the dedicated skill is missing, use the manual social experiment template.
5. **If the user gives many possible tasks**, choose one theme and one scenario; do not produce a broad unprioritized list.
6. **If energy or time is unclear**, default to the 60-minute standard flow.

## Depth Control

Use the smallest depth that achieves the user's purpose.

| Depth | When | Output |
|---|---|---|
| `minimum` | Low-energy day or quick check-in | One signal, one judgment, one language sentence. |
| `standard` | Normal daily run | Full daily loop, concise. |
| `deep` | Weekend, scenario deep dive, or asset creation | Research-backed scenario analysis and weekly asset draft. |
| `exhaustive` | User wants a full scenario harness | Delegate to `scenario-harness-architect`. |

## Weekly Theme Defaults

Use `references/weekly-themes.md` when the user asks for theme ideas. Prioritize themes that produce market-visible assets:

1. High-concurrency transaction systems.
2. AI agent productization.
3. IM / real-time systems.
4. Risk control / authentication / security.
5. Frontend complex interaction and design systems.
6. Game systems and economies.
7. Embedded / IoT constraints.
8. Workplace social practice for technical people.

## Output Discipline

Always make the user's judgment explicit. Avoid turning the report into passive notes.

Every daily or weekly output should include:

- **Core judgment**: the user's current position.
- **Trade-off**: what is gained and what is sacrificed.
- **Evidence**: which signal, market item, scenario fact, or social case supports it.
- **Action**: what to do next.

Do not overfill the day. If the plan becomes too broad, compress it back to:

`one theme -> one scenario -> one judgment -> one expression -> one calibration -> one action`

## Anti-Patterns

Avoid these patterns:

- Turning the workflow into a generic habit checklist.
- Consuming Hacker News or GitHub Trending without extracting a judgment.
- Doing scenario analysis without constraints, trade-offs, or failure modes.
- Practicing English/Japanese with unrelated sentences.
- Looking at job posts until the user becomes anxious.
- Reflecting on social practice without designing a real action experiment.
- Producing many notes but no weekly or monthly asset.
- Letting AI make final human judgments without marking assumptions.

## Templates

Use `references/templates.md` for daily, weekly, monthly, market, social-practice, and language templates.

Use `references/dependency-contracts.md` to understand how the orchestrator should integrate outputs from dependent skills.
