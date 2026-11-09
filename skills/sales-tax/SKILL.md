---
name: sales-tax
description: >-
  Prepares estimated sales tax by US state from recorded store tax data for the entire previous fiscal year or last completed quarter. Use when the merchant invokes sales tax or requests a state-by-state sales tax estimate for either period. Does not determine legal liability or file returns.
---

# Sales tax

Prepare a read-only, state-by-state estimate based on recorded sales taxes, not a determination of tax owed. Do not change tax settings, file returns, send reports, or initiate payments.

## Resolve the reporting period

- Support the entire previous completed fiscal year and the last completed quarter. Honor explicit date ranges and fiscal-quarter requests.
- If no period is specified, ask one focused question offering previous fiscal year or last completed calendar quarter. Do not silently run both.
- For a fiscal period, use fiscal-year boundaries explicitly provided by the merchant or previously recorded in memory. If unavailable, ask for the fiscal-year start date or the exact start and end dates. Never assume fiscal year means calendar year. Support non-calendar and week-based fiscal years by asking for exact boundaries when necessary.
- “Last quarter” means the most recently completed calendar quarter unless the merchant explicitly requests fiscal quarters or has established that convention. State this choice.
- Compute boundaries using exec_js, the current date, and the store's current timezone. Use explicit date boundaries covering the full period, not a rolling year or rolling 90 days. Display inclusive start and end dates and the timezone.

## Retrieve and verify

1. Read the legal system skill. Consult current Help Center documentation for tax-report definitions, coverage, refunds, and any report access or export requirements used. Starting references, to verify when running:
   - https://help.shopify.com/en/manual/reports-and-analytics/shopify-reports/report-types/default-reports/finances-report
   - https://help.shopify.com/en/manual/taxes/tax-reports
2. Use store_analytics_agent for historical reporting. Send a self-contained request with the exact period, store timezone and currency, US-only scope, and grouping by destination state. Request recorded sales taxes net of recorded tax refunds/reversals, including item and shipping taxes, plus net sales if supported. Request marketplace/filed-by-channel separation if supported. Ask it to identify supported fields, coverage, attribution method, and limitations rather than invent dimensions or queries. Do not request a visualization unless the merchant requested one.
3. Do not assume access to the specialized United States sales tax report or equate a general analytics query with it. If a requested tax-specific breakdown is unavailable, report only the supported results and identify the missing breakdown. If no reliable state-tax data is accessible, use search_admin_routes to locate the relevant report and request its export from the merchant. Do not manufacture estimates using generic state tax rates or sales multiplied by an assumed rate.
4. For merchant-supplied report exports, inspect headers and definitions before aggregating. Preserve original exports. Obtain complete coverage for the entire requested period, not just a preview or capped rows. Follow verified report export limits; never assume a single specialized report CSV covers a quarter or year. For explicit export requests, ask the analytics agent to export the supported format rather than rebuilding its export.

## Calculate and check

- Aggregate monetary values with integer minor units or other precision-safe arithmetic in exec_js. Reconcile state sums with the comparable US total using identical period, channel, refund, and destination scope.
- Sum distinct tax components once, including state, county, city, district, and shipping taxes when present. Do not sum repeated sales values across jurisdiction tax rows. Do not subtract refunds a second time from an already net tax metric.
- Label the result “Estimated sales tax based on recorded taxes.” It is not a balance due: recorded taxes may already have been remitted, and do not establish registration obligations or legal liability.
- When available, show marketplace/filed-by-channel taxes separately. Do not label the remainder unpaid or owed. If responsibility or remittance status is unavailable, say so rather than subtracting an assumed amount.
- Use documented destination attribution and disclose material limitations, including missing US state attribution, omitted channels, or unavailable refund detail. Keep unknown-state amounts visible for reconciliation. Show DC and territories separately from the 50 states if present.
- Include every state with returned activity. Do not imply an absent state has zero tax unless complete source coverage establishes that; if displaying all 50 states, mark unverified absences as “No recorded activity in source” rather than tax-exempt or no obligation.
- Separate non-tax fees when the source permits. Otherwise disclose material fee inclusion if documented for that source.
- Never assert nexus, rates, filing deadlines, compliance, or actual tax owed from this report. For legal liability questions, follow the legal skill and route the determination to a qualified tax professional.

## Present the result

Read the building-artifacts system skill and render any returned analytics_queries as required. Provide the exact period, currency, state-level estimated recorded tax, US total, available marketplace separation, and a short explanation of the basis and material coverage gaps. Include net sales and item/shipping tax breakdowns only when reliably available. Cite the documentation actually retrieved.

If data is incomplete, label the result partial and specify the missing period or scope; do not call it an entire-year or complete-quarter estimate. When blocked, explain the specific missing input or data needed to finish. Completion means the complete requested period is covered and totals reconciled, or the limitation is clearly reported.
