---
name: following-up-leads-and-trials
description: >-
  New lead, enquiry, prospect, website contact, trial class, intro class, free trial,
  trial follow-up, trial conversion. Records a new enquiry, replies quickly, books a
  trial into a session, and works the follow-up after the trial: same day or within 24
  hours, then 48 hours, then days 3 and 7. Reports how trials and sign-ups convert, and
  which ads bring leads. Use when the person mentions a new enquiry, wants to book or
  follow up a trial, or asks who tried a class and has not enrolled.
license: see LICENSE
metadata:
  version: "1.0.0"
  tools: "get_business_overview search_people list_programs get_program get_schedule get_session get_report get_help"
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

Use this skill for people who are not yet enrolled: a new enquiry, a trial to book, a trial that happened, and the reports that show how well enquiries turn into members.

It does not cover:

- Enrolling the family once they say yes. Use managing-enrollments.
- Families already enrolled who are drifting away. Use keeping-families-enrolled.
- General announcements. Use messaging-families.

## Ask before acting

- For a new lead: the parent's name, an email or phone, and what they asked about. A child's age is optional.
- For a trial: which program and which session, and whether the trial is free or was already paid.
- Any offer the owner allows. Do not invent an offer or a price.
- Before any message goes for approval: who receives it and whether it is a text or an email.

## Steps

### A new enquiry

1. Call `search_people` first. The family may already be in Hurdle, and a second record splits their history.
2. If they are new, call `create_lead`. It saves on the first call.
3. Reply within the hour. Draft a short answer to what they asked, with the next step: a trial or the program's sign-up page. Send it with `send_message` using the id a tool returned for that person. It waits for approval in Hurdle.
4. Call `list_programs` and `get_program` for the facts in the reply: ages, times, price. Quote them as the tools return them.

### Book a trial

1. Call `get_schedule` to find sessions of the program, then `get_session` to check there is room.
2. Call `add_session_attendee` with the reason set to trial. Show the preview, wait for a yes, then confirm.
3. Offer reminders for the day before and for 2 hours before. They cannot be scheduled from here. Offer `create_task` for each one.

### After the trial

1. Call `get_report` with trial_conversion. It lists who tried a class, who enrolled, who is still deciding, and when each family was last contacted.
2. Work the list by how long ago the trial was:
   - Same day or within 24 hours: thank them and ask how it went. Give the way to enroll.
   - 48 hours: answer the likely question, such as schedule or price.
   - Day 3: a short check-in.
   - Day 7: a last note, then stop.
3. Skip a family that was contacted at this step already. Skip a family marked as not continuing.
4. Send with `send_message` to the parent's id. It waits for approval in Hurdle.
5. Call `add_person_note` to record what the family said, such as "wants Saturdays".
6. Offer `create_task` for the next touch. Nothing here will send it later.

### How leads and trials convert

1. Call `get_report` with trial_conversion for trials, conversion for sign-ups, and ads for which ads bring leads.
2. Quote the report's own rates. Do not work out a rate from the rows.

## Defaults

- Answer a new lead within the hour. For context only: leads contacted within an hour were about 7 times more likely to qualify (Harvard Business Review, 2011).
- Trial reminders: the day before, and 2 hours before.
- Follow-up after a trial: same day or within 24 hours, then 48 hours, then day 3, then day 7. After day 7, stop.
- When the owner allows an offer, waive a fee, such as a registration fee. Do not cut tuition. A waived fee is a one-time cost, while cheaper tuition repeats every month and is unfair to families paying full price.
- Messages go to the parent or guardian. Texts go out between 9am and 8pm in the business's time zone.

## Gotchas

- A trial is not a registration. It appears on the trial conversion report and not on the sign-up conversion report.
- Adding a trial attendee creates no charge. A paid intro class is paid on the program's page. Do not tell a family it is free unless the program says so.
- A program can limit trials to one per person and limit trial spots per class. If the tool refuses, pass on the reason.
- Hurdle can send its own enroll reminder after a trial, to families marked present. Check the last contact in the report before adding another message.
- A family that missed the trial needs a different message: offer another date. Do not ask how the class went.
- A sign-up or trial link in a message is one a tool returned for that program. The business's homepage is not a sign-up page.
- The same `create_lead` input sent twice in a short time returns the first result. It is not a second lead. Do not repeat the call to make sure.
- A message to more than one family still waits for approval. Report it as waiting, not sent.
