# JD Evidence Rules

## Valid JD checklist

A job description counts as valid only when all conditions are true:

1. It has a real company or hiring entity.
2. It has a specific job title.
3. It includes responsibilities or requirements body text.
4. It has a posted or updated date within the last 30 days.
5. It has a source URL or user-provided evidence.
6. It is highly relevant to the user's target direction.
7. It is not a duplicate of another included JD.

## Candidate JD evidence table

Use this table for all candidate JDs:

| JD | Company | Role | Location | Date | Source | Included | Reason |
|---|---|---|---|---|---|---|---|

## Exclusion reasons

Use explicit exclusion labels:

- stale-date: older than 30 days
- missing-date: no posted or updated date
- no-body: listing has no useful responsibilities/requirements text
- weak-relevance: role is not close enough to the target direction
- duplicate: same company and materially same role as another JD
- unverifiable: cannot verify source or authenticity
- non-jd: article, training content, marketing page, or secondhand summary

## Strong conclusion threshold

A strong market conclusion requires:

- at least 6 valid JDs
- a frequency count or representative JD ids
- clear explanation of sample limitations

If the threshold is not met, output an insufficient-evidence report.
