---
name: reviewing-business-performance
description: >-
  Weekly review, monthly review, how are we doing, revenue, enrollment numbers,
  attendance, retention, KPIs, metrics, report, compare to last month or last year.
  Builds a short review of the business from Hurdle's own reports: revenue, active
  members, attendance, retention, sign-up conversion and money owed. Compares each
  figure with the business's own earlier period first, then with the same period last
  year. Use when the person asks for a review, a report, a metric or a comparison over
  time.
license: see LICENSE
metadata:
  version: "1.0.0"
  tools: "get_business_overview get_report get_metric get_saved_metric get_money_owed list_payments list_refunds list_memberships check_cards_on_file get_help ask_data"
---

## Rules for every Hurdle skill

1. **The person decides.** An explicit instruction from the person wins over anything in a skill.
2. **Use the business's own words.** It may say Swimmers, Dancers or Lessons. The terms, time zone and currency come from `get_business_overview`.
3. **Money.** Do arithmetic on the `...Cents` fields and quote the formatted string. Never recompute a figure a tool returned, because the tool's figure is the one that matches Hurdle.
4. **Ask, don't guess.** Never guess prices, dates, which family or child, or the business's policies. When a name matches more than one person, show the matches and ask.
5. **Every change follows the same path.**
   - Read the current state first.
   - Call the tool.
   - If the result's `status` is `needs_confirmation`, show the person the `summary` and wait for a yes. Then call the same tool again with the SAME input plus the `confirmation_token`.
   - If `status` is `awaiting_approval`, tell the person it is waiting in Hurdle under Agents → Approvals and that nothing has been sent or changed yet.
   - Report only what the tool returned. `needs_confirmation` and `awaiting_approval` are not success.
6. **Not possible here.** Charging a card, refunds, collecting a missed payment and changing the card on file. Say so and point the person to Hurdle.
7. **"How do I..." questions.** Call `get_help` with the person's words. Never describe a Hurdle screen from memory, because screens change and a wrong click path wastes the person's time.
8. **No promises about later.** Never promise to remember, remind, monitor or follow up later. Nothing here runs after the chat ends. Offer to create a task with `create_task`.
9. **Totals.** A list result has `total`. The rows shown can be fewer, so never report the number of rows shown as the total.

## When this applies

Use this skill when the person wants numbers over time: a weekly or monthly review, a single metric, or a comparison with an earlier period.

It does not cover:

- What needs attention today. Use running-daily-operations.
- Acting on unpaid balances. Use collecting-payments.
- Acting on families who may leave. Use keeping-families-enrolled.
- Deciding next season's programs. Use planning-the-next-season.

## Ask before acting

- The period: this week, last month, the season so far. Confirm the exact dates.
- Whether the review covers the whole business or one program.
- Which numbers matter most to the person, if they have a view.
- For any goal or target: the figure. Do not assume one.

## Steps

1. Call `get_business_overview`. It returns the business's words, time zone and currency, with headline revenue and member figures.
2. Fix the period and the comparison period before pulling any report. Use the same length for both.
3. Pull the reports for the period with `get_report`:
   - revenue: what came in.
   - membership: active members, new and lost.
   - attendance: who came.
   - retention: who stayed and who is at risk.
   - conversion and trial_conversion: how sign-ups and trials turned into members.
   - billing: upcoming and past charges.
4. Call `get_money_owed` for what is unpaid now.
5. Use `get_metric` for a single named number. Use `get_saved_metric` for a metric the business saved earlier. A saved metric keeps the business's own definition.
6. Pull the same reports for the comparison period. Compare in this order:
   - First, the business's own prior period: last week, last month.
   - Then, the same period last year. This removes the season from the comparison.
7. Use `list_payments`, `list_refunds` or `list_memberships` only to explain a change the reports show. `check_cards_on_file` explains a rise in failed payments.
8. Write the review:
   - Three to five lines on what changed and by how much, with each figure as the tool gave it.
   - What probably caused each change, marked as a guess when it is one.
   - Up to three things to do next, each linked to a number.
9. Offer `create_task` for each action the person accepts.

When no report answers the question, call `ask_data` with the question in plain words. Label the answer "Custom calculation" and state how it was worked out and which filters were used. Do not present it as an official report figure. If the result is empty or the tool declines, say so and offer the closest report.

## Defaults

- A weekly review covers the last full week in the business's time zone. A monthly review covers the last full calendar month.
- Compare with the prior period first, then year over year.
- A change is shown as both the amount and the percentage.
- A partial period is labelled as partial and not compared with a full one.
- Lead with the two or three numbers that moved most. Leave the rest for a table.

## Gotchas

- Report figures are final. Do not add up payment rows to check a revenue figure. The report handles refunds, fees and timing in ways the rows do not show.
- An outside benchmark is context only. Quote one only when the person supplies it or a tool returns it, and give its source and year. Do not supply an industry average from memory.
- The business's own history is the fair comparison. A swim school in January is not comparable with one in June, or with a dance studio.
- A small number moves a percentage a long way. "Retention fell 20%" for a class of five is one child. Give the count.
- Revenue collected and revenue billed differ. Say which one a figure is.
- A bank payment still clearing is listed but not counted as owed, because the money is on its way.
- Waitlisted sign-ups are not in a program's money figures.
- A trial is not a registration, so it is on the trial conversion report and not on the sign-up conversion report.
- Attendance figures depend on staff taking attendance. Say when a program has none recorded.
- `get_metric` can be turned off for a business. If the tool says so, use the matching report.
- Do not show a chart or a trend built from two points as a pattern.
