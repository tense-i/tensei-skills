---
name: boss-market-calibrator
description: analyze current job market demand for a user-specified career direction using real job descriptions from the last 30 days. use when the user asks to evaluate a role, technology direction, career path, market demand, salary value, job requirements, or skill gap based on boss直聘, lagou, liepin, linkedin, indeed, company career pages, or user-provided jd text. must collect and analyze at least 6 valid recent jds before making strong conclusions; every conclusion must be grounded in jd evidence.
---

# Boss Market Calibrator

## Purpose

Calibrate the user's career direction against the current market using real job descriptions from the last 30 days. Do not rely on memory, generic career advice, or trend articles.

Use this skill to answer:

- What is the market currently paying for?
- What capabilities appear repeatedly in real JDs?
- Which requirements are entry tickets, core capabilities, differentiators, or noisy requirements?
- What is the user's gap against the target direction?
- What should the user do in the next 30 days?
- What project or portfolio asset should the user build to prove market-valued ability?

## Hard Rules

1. Use only real JD evidence from the last 30 days.
2. Collect at least 6 valid JDs before making strong conclusions.
3. Every key conclusion must cite or reference specific JD ids.
4. Do not count undated, stale, duplicate, weakly related, or content-free job listings.
5. If fewer than 6 valid JDs are found after search expansion, output an insufficient-evidence report instead of strong conclusions.
6. Distinguish entry-ticket skills, core role skills, differentiators, and low-priority/noisy requirements.
7. Always disclose sample limitations and source bias.
8. Prefer 8-12 valid JDs when available; 6 is the minimum acceptable sample.

## Valid JD Criteria

A JD is valid only if it has:

- company or hiring entity
- job title
- responsibilities or requirements body
- posted or updated date within the last 30 days
- source URL or user-provided evidence
- clear relevance to the user's direction
- no duplicate with another included JD

If the user provides JD text directly, count it only when the user provides or clearly states that the JD is from the last 30 days.

## Workflow

### Step 0: Parse the direction

Extract:

- target role or direction
- geography
- seniority
- language or region preference
- platforms to prioritize
- whether the user provided JD text
- whether user background is available for gap analysis

Default assumptions:

- geography: mainland China unless specified
- time range: last 30 days
- target sample: 8-12 JDs
- minimum valid sample: 6 JDs

### Step 1: Collect JD evidence

If the user provided at least 6 valid recent JDs, use them first.

Otherwise, search for JDs using:

1. major recruiting platforms
2. company career pages
3. search engine queries combining role, region, and recency
4. adjacent role names if direct results are insufficient

Run up to three expansion rounds:

- Round 1: expand keywords and synonyms
- Round 2: expand sources, platforms, and company career pages
- Round 3: expand adjacent role titles while preserving relevance

Never use invalid JDs just to reach 6.

See `references/search-playbook.md` for search expansion patterns.

### Step 2: Build JD evidence table

For every candidate JD, record:

- JD id
- company
- job title
- location
- source
- posted or updated date
- responsibilities summary
- requirements summary
- included or excluded
- exclusion reason if excluded

See `references/jd-evidence-rules.md` for validation and exclusion rules.

### Step 3: Code the JDs

Tag each valid JD across:

- technical stack
- business scenario
- engineering capability
- AI/data capability
- seniority/years
- language requirement
- soft skills
- industry background
- differentiators
- compensation/level signals if available

See `references/capability-taxonomy.md` for the capability coding taxonomy.

### Step 4: Frequency analysis

Create a capability frequency matrix:

- capability
- count
- percentage
- representative JD ids
- market interpretation

Do not write broad conclusions without count and JD id support.

### Step 5: Market capability layering

Classify capabilities into:

- L1 Entry-ticket capabilities
- L2 Core role capabilities
- L3 Differentiators / salary premium capabilities
- L4 Noisy or low-priority requirements

### Step 6: User gap analysis

If user background is available, compare it against JD evidence:

- matched capabilities
- missing capabilities
- weakly evidenced capabilities
- resume/project evidence gaps
- strongest leverage point
- one 30-day priority

If user background is not available, provide a generic target capability map and ask for resume/project context for a second-pass calibration.

### Step 7: Portfolio reverse engineering

Recommend 1-3 portfolio assets or project directions that prove the most market-valued capabilities from the JD evidence.

Each recommendation must specify:

- which JD-backed capability it proves
- why it is stronger than a generic demo
- minimum scope
- observable proof points
- how to describe it in a resume or interview

### Step 8: Output report

Use the standard report format from `references/report-templates.md`.

## Confidence Levels

- A: 10+ valid recent JDs, diverse sources, highly consistent role definition.
- B: 6-9 valid recent JDs, reliable sources, mostly consistent.
- C: 6 valid JDs but concentrated sources or mixed role definitions.
- D: fewer than 6 valid JDs. Do not make strong conclusions.

## Insufficient Evidence Behavior

If fewer than 6 valid JDs are found after expansion:

1. State that the sample is insufficient.
2. Show found JDs and excluded JDs.
3. Explain why each excluded JD was invalid.
4. Suggest how to proceed:
   - user provides more JD text
   - broaden geography
   - broaden role keywords
   - extend time range only if user explicitly allows it

Do not output a normal market calibration report.

## Anti-Distortion Rules

Never:

- rely on memory for market trends
- use stale or undated JDs to reach the minimum sample
- use trend articles as JD evidence
- generalize from a single big-company JD
- ignore region, seniority, or source bias
- output advice without an evidence table
- hide uncertainty when samples are thin

Always:

- show JD samples
- show dates and sources
- show inclusion/exclusion decisions
- compute frequency
- tie conclusions to JD ids
- disclose limitations
- downgrade to insufficient-evidence mode when needed
