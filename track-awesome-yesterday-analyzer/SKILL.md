---
name: track-awesome-yesterday-analyzer
description: analyze yesterday's updates on trackawesomelist by starting from https://www.trackawesomelist.com/, retrieving the actual updated awesome lists and their newly added items, then producing a long-form structured chinese report. use when the user asks to analyze yesterday's track awesome list, yesterday's awesome list updates, recent trackawesomelist changes, or wants a deep report that first covers all updates and then selects the most important ones for deeper interpretation.
---

# Track Awesome Yesterday Analyzer

Use this skill to turn Track Awesome List's yesterday update stream into a structured Chinese analysis report. Start from the homepage URL the user provides or from `https://www.trackawesomelist.com/`. Always inspect the real updated content from yesterday rather than guessing from memory or giving a curated summary only.

## Workflow

1. Open Track Awesome List and locate the section that corresponds to yesterday's updates.
2. Enumerate every awesome list updated yesterday.
3. Open each updated list page and extract the newly added entries for that day.
4. Build a complete update inventory first.
5. Select the few additions that are genuinely most worth deeper analysis.
6. Write a long-form Chinese report using the template in `references/report_template.md`.

## Retrieval rules

- Always use live web retrieval. Do not rely on stale memory for yesterday's content.
- The user input is just the homepage URL. You must navigate from there and discover yesterday's updated lists yourself.
- Yesterday means the calendar day immediately before the current date in the user's timezone.
- The first section of the report must cover yesterday's actual updates broadly, not a hand-picked subset.
- Distinguish between:
  - the awesome list that was updated
  - the newly added items inside that list
- Prefer the newly added items and the surrounding list context as the factual basis of analysis.
- If a list page is ambiguous, verify with the corresponding GitHub diff, commit view, or rendered list page before summarizing.

## Selection rules for deep analysis

After completing the full inventory, pick a limited set of additions for deep analysis. Use these signals:

- appears across an important tool or workflow shift
- reveals a new category forming or accelerating
- is unusually actionable for builders, product engineers, or technical decision makers
- has strong implications for ecosystem direction, not just novelty

Do not simply pick the most famous project names. Choose the additions with the strongest explanatory value.

## Writing rules

- Write in Chinese.
- Produce a long-form, structured report, not short bullets.
- Keep the opening overview concise, then expand the analysis in later sections.
- Separate factual inventory from interpretation. Make it clear what was updated and what your inference is.
- Do not call the first section “精选”. The first section must be a complete overview of yesterday's updates.
- When counts are available, include them, but do not invent exact numbers if you could not verify them.
- Use citations for factual claims whenever web sources were used.

## Required report shape

Follow `references/report_template.md` closely.

The report must include these parts in order:

1. Yesterday update snapshot
2. Full update inventory by awesome list and newly added items
3. Selected additions worth deep analysis
4. Signal interpretation and ecosystem reading
5. Practical follow-up suggestions

You may add small supporting subsections, but do not remove these five parts.

## Quality bar

Before finalizing, check all of the following:

- Did the report start from yesterday's full update set rather than a curated subset?
- Did each inventory entry describe the updated awesome list and the newly added items?
- Did the deep-analysis section clearly explain why those items matter?
- Did the ecosystem section synthesize cross-list signals instead of repeating item descriptions?
- Is the output substantial enough to be read as a standalone interpretation report?
