---
name: aov
description: >-
  Generates an AOV (average order value) report for all reportable orders in the last 30, 60, or 90 days, broken down by traffic source. Use when the merchant invokes AOV or requests this traffic-source AOV report. Defaults to 30 days when no period is specified.
---

# AOV

Generate the report when invoked, without asking permission to retrieve data.

1. Use the requested lookback of 30, 60, or 90 days. Default to 30 days when omitted. If multiple supported periods are requested, report each separately. If a different period is explicitly requested, ask the merchant to choose 30, 60, or 90 days rather than silently substituting one.
2. Use the shop's current timezone and currency. Interpret each lookback as the last N complete calendar days ending yesterday, and state the exact inclusive dates. Use `exec_js` for date arithmetic with explicit timezone handling, not a fixed UTC offset.
3. Read `/workspace/agents/skills/system/building-artifacts/SKILL.md` before presenting analytics. Retrieve the report with `store_analytics_agent`, giving it a self-contained request with the exact date range, shop timezone, currency, and the requirements below. Do not use Admin order reads to reconstruct historical reporting.
4. Request the native average order value metric and order count, grouped by traffic referrer source, with an independently calculated all-orders overall AOV and order count for the same dates. Include all sources and all reportable orders without paid-status or sales-channel filters. Retain direct, unknown, and missing-source orders, including orders without online traffic attribution. Do not substitute a marketing-only report or exclude unattributed orders. Ask the analytics agent to verify the supported source dimension and coverage and explain any limitation. Use the report's last-interaction referrer attribution, not multi-touch credit that duplicates orders. Do not substitute referrer host or sales channel for traffic source without explaining the limitation.
5. Use Shopify's AOV definition: original product gross sales minus original discounts, divided by orders, excluding post-order adjustments. Do not use total sales divided by orders or average the source-level AOVs. Standard sales-report exclusions apply, including test and deleted orders; pending, unpaid, and canceled orders are not to be filtered out. If metric behavior needs verification, consult the sales report documentation below.
6. Render the executed `analytics_queries` as analytics cards following the building-artifacts skill. Include every returned traffic-source row, sorted by AOV descending, with order counts for context. Format AOV in the shop currency to two decimal places. State the overall AOV, date range, and a brief useful finding grounded in returned data. Label missing sources as Unattributed while keeping explicitly Unknown and Direct sources distinct. If a source grouping cannot cover all reportable orders, disclose that beside the overall all-orders result instead of claiming exhaustive coverage.
7. If there are no orders, state that AOV is unavailable, not zero. If data retrieval fails or coverage is incomplete, explain what is unavailable and do not invent values. Do not claim a persistent saved report was created: invoking this skill generates a report in the conversation. Save or export only if separately requested, using the appropriate tools.

Reference for metric definition and traffic referrer attribution:
https://help.shopify.com/en/manual/reports-and-analytics/shopify-reports/report-types/default-reports/sales-report

Example: `AOV last 60 days` generates the all-orders AOV summary and traffic-source breakdown for the last 60 complete days.
