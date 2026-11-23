---
name: running-daily-operations
description: >-
  Morning check, daily rundown, what needs attention today, front desk jobs. Runs the
  opening check in a fixed order (today's sessions with booked counts, sessions with no
  coach, trials today, failed and overdue payments, open tasks, unanswered messages) and
  handles quick jobs: look up a family, record a trial, drop-in or make-up, book an
  appointment, add or extend make-up credits. Use when the person asks what is happening
  today, wants a start of day summary, or needs a quick action for a family in front of
  them.
license: see LICENSE
metadata:
  version: "1.0.0"
  tools: "get_business_overview get_schedule get_session get_money_owed list_tasks get_inbox_summary search_people get_person get_availability get_appointment_slot get_credit_ledger get_help"
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

Use this skill for the start of day check and for quick jobs at the front desk: finding a family, adding someone to today's session, booking an appointment, fixing credits.

It does not cover:

- Chasing the unpaid balances the check turns up. Use collecting-payments.
- Cancelling or moving a session, or a coach who is out. Use handling-schedule-changes.
- Enrolling someone in a whole program. Use managing-enrollments.
- Following up after a trial. Use following-up-leads-and-trials.

## Ask before acting

- Which person, when `search_people` returns more than one match.
- Which session, when the person names a class that runs more than once today.
- Why the person is being added to a session: trial, drop-in, make-up or guest. The reason is recorded and it changes what the family is told.
- For credits: how many, the reason, and the expiry. Ask for the business's make-up policy if the person has not stated it.

## Steps

### Opening check

Run the check in this order. The order goes from what happens soonest to what can wait.

1. Call `get_business_overview` for the business's words and time zone, unless it was already called in this chat.
2. Call `get_schedule` for today. List each session with its time, venue, staff and booked count against capacity. Point out sessions that are full and sessions with nobody booked.
3. From the same result, list sessions with no coach assigned. These need a person before the doors open.
4. List trials today. If the schedule result does not mark trials, call `get_session` on today's sessions and look for attendees added as a trial. Name the parent, since the parent is who staff will greet.
5. Call `get_money_owed`. Report the failed and overdue amounts the tool returns, and how many families. A bank payment still clearing is on its way and is not failed.
6. Call `list_tasks` for open tasks. Put overdue and due-today tasks first.
7. Call `get_inbox_summary` for unanswered messages.
8. Close with a short list of what needs a person today, most urgent first. Offer the next step for each, or `create_task` to hand it to staff. Do not start any change the person did not ask for.

### Look up a family

1. Call `search_people` with the name, email or phone.
2. Call `get_person` on the chosen match. Ask for the extra sections only when the question needs them: payments, attendance, appointments, credits, what they owe, cards on file.

### Record a trial, drop-in or make-up

1. Find the session with `get_schedule`, then check space with `get_session`.
2. Call `add_session_attendee` with the person, the session and the reason. This adds them to that one session. It creates no registration and no charge.
3. Show the preview summary, wait for a yes, then confirm.
4. To undo it, use `remove_session_attendee`. It removes only people who were added this way, not enrolled members.

### Book an appointment

1. Call `get_availability` for the service and date range. Offer the open slots in the business's time zone.
2. Call `get_appointment_slot` when the person wants the detail of one slot.
3. Call `book_appointment` for a member, or `book_walk_in` for someone who walked in. Use `cancel_appointment` to cancel one.
4. Show the preview, wait for a yes, then confirm. Read out what the booking uses, such as a credit, if the preview says.

### Make-up credits

1. Call `get_credit_ledger` to see what the person has now and when it expires.
2. Call `adjust_credits` to add or remove credits, with the reason. Call `extend_credits` to move the expiry.
3. In the answer, state the new balance and the expiry date. A credit with no stated expiry leads to an argument later.

## Defaults

- "Today" means today in the business's time zone.
- The opening check reads only. It changes nothing.
- A make-up credit is stated with its expiry. If the business has no expiry rule, ask the person for one before adding the credit.
- When the day has many sessions, show the ones that need attention and give the count of the rest.

## Gotchas

- A booked count is not attendance. Attendance exists only after staff take it.
- A closed session, on a day the business is closed, stays on the schedule and shows as closed. Do not report it as a session with no coach.
- A paid intro class needs a payment, which cannot be taken from here. Add the person only when the trial is free or was already paid, and say which.
- Staff can book over a conflict in Hurdle. If a session shows the same coach twice at one time, report it. Do not fix it unasked.
- Use `complete_task` only for a task the person says is done. Finishing a task for them hides work that may not have happened.
- A tool can refuse a booking, for example when the slot was taken a moment ago. Pass on the reason and offer the next open slot.
