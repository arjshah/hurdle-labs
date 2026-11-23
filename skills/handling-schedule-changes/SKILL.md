---
name: handling-schedule-changes
description: >-
  Holiday, closure, snow day, cancel a class, reschedule a session, move a class time,
  coach out sick, substitute, make-up class. Picks the right change for the situation: a
  closure for a holiday or closed day, a status change or reschedule for one session, a
  comparable substitute before any cancellation when a coach is out. Offers make-up
  credit, and prepares the notice to families for approval in Hurdle. Use when something
  on the schedule will not run as planned and families or staff need to know.
license: see LICENSE
metadata:
  version: "1.0.0"
  tools: "get_business_overview get_schedule get_session list_closures list_staff get_credit_ledger get_request_status get_help"
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

Use this skill when a session, a day or a run of days will not happen as planned: a holiday, a closed building, bad weather, a moved class, or a coach who is out.

It does not cover:

- Adding one person to a session. Use running-daily-operations.
- Building next season's schedule or adding a permanent class time. Use planning-the-next-season.
- Messages unrelated to a schedule change. Use messaging-families.

## Ask before acting

- Exactly which dates and which sessions. Read them back before any change.
- Whether the change is for the whole business, one venue or one room.
- Whether families should be told, and by text or email.
- The business's make-up policy for a cancelled session. If there is none, use the Defaults.
- For a substitute: which staff members the owner considers able to teach that class.

## Steps

1. Call `get_schedule` for the dates involved. List the sessions affected, with booked counts.
2. Call `list_closures` to see whether the dates are already closed.
3. Pick one change by the shape of the problem:
   - **A holiday or closed day, or a run of days.** Call `add_closure`. One closure closes every session on those dates for the place it covers. It waits for approval in Hurdle.
   - **One session will not run**, for a reason that is not a closure. Call `update_session_status`.
   - **One session moves** to a new time or place. Call `reschedule_session`.
   - **A coach is out.** Go to step 4 before considering a cancellation.
   - **Several sessions change** and it is not a closure. Call `bulk_edit_sessions`. It waits for approval in Hurdle.
4. For a coach who is out:
   - Call `list_staff`, then `get_schedule` for that time, to find staff who are free.
   - Suggest a comparable substitute: someone the owner says can teach that class. Ask if unsure. Do not judge qualifications from a job title.
   - Call `change_session_staff` with the substitute.
   - Cancel the session only when the person confirms nobody can cover it.
5. Show the preview summary, wait for a yes, then confirm. For a change that waits for approval, tell the person where to approve it.
6. Offer the make-up. Use the business's policy. Options the tools support:
   - Add the family to another session as a make-up with `add_session_attendee`.
   - Add a make-up credit with `adjust_credits`, or move an expiry with `extend_credits`. Check the balance with `get_credit_ledger` first, and state the expiry.
   - Add a replacement session with `create_session`.
7. Tell families. Use `get_session` to see who is booked. Send with `send_message` to the program, the team or the attendee ids. It waits for approval in Hurdle.
8. Report what changed, what is waiting for approval, and what was not done. Use `get_request_status` if the person asks whether an approval went through. Offer `create_task` for anything left for staff, such as calling a family that has not replied.

## Defaults

- A cancelled session earns a make-up credit, not cash, unless the business's policy says otherwise.
- Same-day changes go by text, because families are already on their way. Planned changes go by email, which holds more detail.
- Try a substitute before cancelling. Families signed up for the class, and a covered class keeps the promise.
- A notice names the session, the date, what changed and what the family should do next.

## Gotchas

- Anything that notifies families waits for approval in Hurdle. This includes a reschedule or staff change that tells attendees, and any change that reaches future sessions. Until it is approved, nothing has changed and nobody has been told.
- Do not split a change to a whole series into many single-session calls to get past approval. The approval exists so the owner sees a change that reaches many families.
- A closure does not change billing and does not create make-ups. If the business wants to make up the day, that is a separate step.
- Some programs run through closures. Camps, clinics, tournaments and tryouts often do, and games keep their schedule. Check the affected list in the summary. Do not assume every session closes.
- A closure over booked appointments has two outcomes: the bookings stay, or they are cancelled and credits returned. Ask which the person wants.
- For a holiday, use a closure, not a cancelled session and not a deleted class time. Families then see the day as closed, and reminders stop.
- If the change itself tells attendees, do not also send a separate message. Two notices for one change confuses families.
- A substitute needs no notice to families unless the business wants one. Ask.
- A text outside 9am to 8pm in the business's time zone is sent only when the owner says it is urgent, such as a cancellation within the next few hours.
- Hurdle warns about a room or a coach that is already booked at the new time, but it does not block the change. Check `get_schedule` for the new time and point out any overlap.
