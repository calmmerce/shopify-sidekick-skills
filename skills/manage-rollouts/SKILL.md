---
name: manage-rollouts
description: >-
  Prepares reviewable Rollout Launch, Event, or Experiment drafts from a supplied name and date or date range. Use when the merchant invokes manage-rollouts or asks to prepare a named rollout with dates. Never saves, schedules, releases, or launches it on the merchant's behalf.
---

# Manage Rollouts

Prepare a new Rollout for the merchant to review, never release it. Creating this skill or invoking it is not authorization to save or activate a rollout.

## Inputs

Accept natural-language inputs: rollout type (Launch, Event, or Experiment), name, start date/time, and an end date/time where applicable. For example:

`/manage-rollouts Event "Holiday weekend" November 27, 2026 at 9 AM through November 30, 2026 at 11 PM America/Los_Angeles`

Accept partial inputs without rejecting the request. Preserve the supplied name and dates. Ask one focused question for missing required decisions, combining related schedule details when helpful. Do not invent a year, start time, end time, traffic allocation, treatment, or end behavior. Use current conversation context when it already supplies them.

## Workflow

1. Read `/workspace/agents/skills/system/rollouts/SKILL.md` on every invocation, then follow its current discovery, specialized drafting, verification, and safety rules. Use only tools actually available in the current conversation. If the required specialized tool cannot be reached, explain the limitation and provide a proposal instead; never substitute generic form tools or a GraphQL mutation.
2. Determine whether the request is a Launch, Event, or Experiment. If unspecified and the goal is unclear, ask the merchant to choose. A single date does not by itself establish the type. Identify the intended resource and change from context or ask for it. A name and dates can begin a proposal, but cannot establish an unknown treatment.
3. Resolve the schedule in the store timezone unless the merchant explicitly supplies another timezone. State the timezone in the review summary. Use date/time computation tools for conversions, relative dates, and duration validation. Ask about ambiguous dates or missing times rather than silently using midnight. Check current Rollouts requirements and limits from the system skill and relevant documentation before drafting; identify invalid or past schedules and ask for corrected dates.
4. Follow the system skill's type mapping: Launch keeps changes, Event uses a launch with an end date and rollback, and Experiment compares control and treatment. Collect the required end date, end behavior, Traffic allocation, and Traffic split as applicable. Do not assume an experiment winner or permanently apply its treatment. If useful, propose specific missing values for confirmation, but do not populate unconfirmed choices.
5. Verify the requested resource, supported change, conflicts, and available options through the current supported read-only Rollouts workflow. Read further system references only when their scope applies. Do not introduce extra discounts, catalogs, theme edits, or checkout changes that the merchant has not requested or confirmed.
6. Once the required details are clear, populate only the reviewable new Rollout form through the specialized Rollouts drafting workflow. For follow-ups, continue only the matching current activity as instructed by the system skill. Preserve unrelated form fields and do not duplicate a draft after an error or an invisible result; inspect or explain the limitation first.
7. Summarize only what the drafting result confirms: name, type, dates with timezone, requested change, allocation/split, and end behavior as relevant. Clearly distinguish a populated unsaved form from a proposal when drafting was unavailable. Tell the merchant that nothing has been saved or released and leave review and saving to them. Explain that saving a future-dated configuration can schedule it, so they should review before saving.

## Approval boundary

Never invoke save, submit, schedule, launch, release, resume, apply-treatment, or other lifecycle-changing tools as part of this skill. Never accept warnings or confirmations for the merchant. Do not automatically release anything when a date arrives or claim a rollout exists merely because its form was populated. If the merchant asks for a lifecycle action, explain that this skill is draft-only and leave the action to them.

## Scope checks

- Matching request: “Prepare an Event named Holiday weekend for November 27 at 9 AM through November 30 at 11 PM.” Resolve the year if missing, establish the treatment and required allocation, and prepare only a reviewable form.
- Missing-input request: “Launch named New look on December 1.” Ask for missing schedule and resource decisions; do not invent them or release anything.
- Non-matching request: “Show last month's top products.” Use the appropriate analytics workflow, not this skill.
- Failure case: If permissions, specialized tools, or valid treatment options are unavailable, state what blocked drafting and what remains missing. Do not claim creation or retry through another write route.
