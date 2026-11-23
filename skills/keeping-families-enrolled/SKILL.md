---
name: keeping-families-enrolled
description: >-
  Retention, churn, at-risk families, drop-offs, missed classes, falling attendance,
  families about to quit, win back. Finds families who may leave from the retention and
  attendance reports, explains the warning signs (two or three absences in a row,
  attendance falling, a new late payment, the first six weeks), and prepares a personal
  check-in for approval in Hurdle. Use when the person asks who is at risk, why families
  are leaving, or how to keep a family that has stopped coming.
license: see LICENSE
metadata:
  version: "1.0.0"
  tools: "get_business_overview get_report get_metric get_person search_people get_program_attendance_breakdown get_help"
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

Use this skill when the person asks which families may leave, wants to reach out to a family that has stopped coming, or asks why members are dropping off.

It does not cover:

- A family that has decided to cancel or pause. Use managing-enrollments.
- A late payment on its own. Use collecting-payments.
- People who tried a class and never enrolled. Use following-up-leads-and-trials.
- Retention as a number in the monthly review. Use reviewing-business-performance.

## Ask before acting

- Which program or group to look at, if the person wants less than the whole business.
- What the business offers a family that is struggling: a make-up class, a different class time, a pause. Do not invent an offer.
- Who should make the contact. A coach who knows the child often does this better than a message.
- Before any message goes for approval: who receives it and whether it is a text or an email.

## Steps

1. Call `get_business_overview` for the business's words and time zone, unless already known.
2. Call `get_report` with retention. It says which members are at risk and why. Use its list as given.
3. Call `get_report` with attendance for the same period, to see who has stopped coming. Use `get_program_attendance_breakdown` when the question is about one program's class times.
4. If the person asks for one retention number, call `get_metric`. If the tool says metrics are not available for this business, say so and use the report.
5. For each family the person wants to act on, find them with `search_people` and call `get_person` with attendance and payments. Look for these signs:
   - Two or three absences in a row.
   - Attendance falling over the last few weeks.
   - A new late payment from a family that used to pay on time.
   - The first six weeks after joining, when habits are not yet set.
6. Group the list by reason, most urgent first. A family with a second missed class this week comes before a family whose attendance has drifted for a month.
7. Suggest one next step per family:
   - A personal check-in from the coach or the owner.
   - A make-up class or a different class time, if the business offers it.
   - A pause if the family needs a break, so they do not cancel.
8. Draft the check-in and send it with `send_message` to the parent's id. It waits for approval in Hurdle.
9. Call `add_person_note` to record what the family said, when the person reports it.
10. Offer `create_task` for a call or a second check-in. Nothing here will follow up later.

## Defaults

- Contact a family within a day of the second missed class in a row. The sooner the contact, the easier it is for the family to come back.
- The first contact is a check-in, not an offer. Ask how the child is doing and whether the time still works.
- One family, one message. A check-in that reads like a mass mailing does more harm than none.
- Offers are for families at risk. A family that is attending and paying gets no discount.
- Messages go to the parent or guardian. Texts go out between 9am and 8pm in the business's time zone.

## Gotchas

- "At risk" is what the retention report says. Do not recompute it from attendance rows or apply a different rule. The owner sees the same list in Hurdle, and two different lists destroy trust in both.
- Attendance signals exist only where staff take attendance. A program with no attendance taken looks like nobody came. Say when the data is missing. Do not call those families at risk.
- An absence during a closure or a pause is not a missed class.
- A late payment and falling attendance together are a stronger sign than either alone. A late payment alone may be only a card problem.
- Do not mention a balance in a check-in message. Mixing "we miss you" with "you owe us" loses the family.
- Do not offer a discount to a family that is not at risk. It costs money and teaches families to ask.
- Do not tell a family they were flagged, scored or reported as at risk.
- A cut in tuition is the owner's decision. Create a task for the owner.
