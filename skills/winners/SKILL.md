---
name: winners
description: >-
  Shows best-selling products. Use when the merchant asks for winners or top sellers by day, month, calendar quarter (Q1, Q2, Q3, Q4), half year (H1, H2), or year-to-date (YTD). Defaults to the top 5, with any merchant-requested positive whole-number limit.
---

# Winners

Produce an on-demand best-seller report. This skill does not schedule reports or change the store.

## Interpret the request

- Default to 5 products. Honor any explicitly requested positive whole-number count without imposing a skill-specific maximum. A count applies to this request, not future requests, unless the merchant explicitly asks to change the default. If a count is invalid, ask for a positive whole number.
- Default ranking is net units sold, descending. Include net sales as context and use it to break unit-count ties, then product title for stable ordering. If the merchant asks for best sellers by revenue, rank by net sales instead and state that choice.
- Aggregate variants at product level unless the merchant explicitly requests variants. Use product identity, not title alone, when grouping if supported, so equal titles do not merge different products.
- Default to YTD if no period is provided. Do not ask for a period just to run the report.
- Use the store's current timezone for reporting periods and show exact dates. Compute boundaries using JavaScript with explicit timezone handling, not mental date arithmetic or the sandbox's default timezone.
- Day means the requested calendar date; an unqualified day/daily request means today. Month means the requested calendar month; an unqualified month/monthly request means the current month to date.
- Q1 is January–March, Q2 April–June, Q3 July–September, and Q4 October–December. H1 is January–June and H2 July–December. Unqualified quarter or half year means the current quarter or half year to date.
- Use an explicitly requested year; otherwise use the current store-local year. YTD runs from January 1 through today. If a past year is explicitly attached to YTD without a cutoff, use the same month/day cutoff in that year, clamping February 29 to February 28 when necessary, and label the cutoff.
- Past named periods use their full calendar range. An ongoing period ends today and is clearly labeled to date. A future period is not replaced by a previous period; explain that it has not started and invite a past/current selection.
- For requests such as “each day last month,” return independent top-N rankings for every requested bucket, not the top N across the combined date range. Requests for all four quarters or both half years likewise require separate rankings per period.

## Retrieve and present

1. Read the available building-artifacts skill before presenting analytics cards. Use `store_analytics_agent` for sales reporting, not current product searches or order-by-order reconstruction.
2. Send a self-contained request specifying exact period boundaries, store timezone, ranking metric, product or variant granularity, count, tie-breaking, and net sales as context. Request no visualization clause unless the merchant explicitly asks for a visualization. For multiple buckets, require the top N independently in each bucket.
3. Honor the requested count even when it requires further retrieval. If a tool/report limit prevents full delivery, state the actual delivered count and limitation, and continue with supported retrieval where possible; never silently cap at 5 or another number. If fewer products have qualifying sales, show only the available products without padding the ranking.
4. Show executed analytics queries as analytics cards using the building-artifacts skill. Briefly summarize the leading product, period, ranking basis, and requested count. Use the report currency, not an assumed currency. Do not invent results, treat missing data as zero, or claim completeness when retrieval is partial.
5. If reporting fails, state the failure plainly and do not substitute guessed rankings. Do not export files unless requested; explicit exports go through the analytics agent's export capability.

Example: “winners top 12 Q2 2026” means the 12 products with the highest net units sold for April 1–June 30, 2026, with net sales alongside them, subject to a to-date cutoff if that period is currently in progress.
