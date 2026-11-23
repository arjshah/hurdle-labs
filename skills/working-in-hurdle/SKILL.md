---
name: working-in-hurdle
description: >-
  Hurdle basics, getting started, "how do I" questions, what can be done from here.
  Explains how Hurdle is organised (programs, registrations, members and families,
  schedule, messages, money), which tool answers which kind of question, and what this
  connection cannot do. Use at the start of any work in Hurdle, when a request fits no
  more specific Hurdle skill, when the person asks how to do something in Hurdle, or
  when they ask to charge a card, refund a payment or change a card on file.
license: see LICENSE
metadata:
  version: "1.0.0"
  tools: "get_business_overview get_help search_people get_person list_programs get_program get_schedule list_staff list_teams list_venues list_tasks get_request_status"
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

Use this skill at the start of any work in Hurdle, for "how do I" questions, and for a request that fits no more specific skill. It also covers requests to move money, which cannot be done from here.

Use a neighbouring skill when the job is specific:

- Today's rundown or a front-desk action: running-daily-operations.
- Who owes money, failed or overdue payments: collecting-payments.
- Enroll, move, cancel, pause, waitlists: managing-enrollments.
- Closures, cancelled or moved sessions, a coach out: handling-schedule-changes.
- New enquiries and trial classes: following-up-leads-and-trials.
- Families who may leave: keeping-families-enrolled.
- Any email or text to families: messaging-families.
- Weekly or monthly numbers: reviewing-business-performance.
- Re-enrollment and next season's programs: planning-the-next-season.

## Ask before acting

- Which person, when a name matches more than one. Show the matches from `search_people` with a detail that tells them apart, and let the person pick.
- Which program, when the request needs one and none was named. List the running programs from `list_programs`.
- Whether the person wants an answer or a change, when the wording could mean either.

## Steps

1. Call `get_business_overview` once at the start. Note the business's own words for members, sessions and teams, its time zone and its currency. Use them in every answer.
2. Sort the request into one of four kinds:
   - A question about the business's data. Answer it from a read tool.
   - A "how do I" question. Call `get_help` with the person's own words and answer from what it returns.
   - A change. Follow the change path in the rules above.
   - Moving money. Say it is not possible from here and that it is done in Hurdle. Call `get_help` if the person wants the steps.
3. Use this picture of Hurdle to find the right tool. Everything starts with a program.
   - **Programs** are what the business sells: a class, a season, a camp, a membership, appointments. `list_programs`, `get_program`.
   - A **registration** is one person signed up for one program. Two children in one program are two registrations.
   - **Members and families** are the people, linked by household. A parent or guardian pays and receives messages for a child. `search_people`, `get_person`.
   - The **schedule** holds the sessions people come to, at venues, with staff. `get_schedule`, `list_staff`, `list_venues`, `list_teams`.
   - **Messages** are emails and texts with families. They are sent only after approval in Hurdle.
   - **Money** is what was paid and what is owed. It can be read here. It cannot be moved here.
   - **Tasks** are the staff to-do list. `list_tasks`, `create_task`.
4. For a request that waits for approval, give the person the place to approve it (Agents → Approvals). If they ask later whether it went through, call `get_request_status` with the request id the tool returned.
5. Answer in one or two plain sentences first. Add a table only when there are several rows to compare.

## Defaults

- Show dates and times in the business's time zone.
- When a request could be a question or a change, treat it as a question. A read costs nothing, and a change the person did not ask for costs trust.
- When `get_help` finds no article, say there is none. Offer `create_task` so a staff member can ask Hurdle support.

## Gotchas

- Only owners and admins can use this connection. A refusal from a tool is one sentence that says what happened and what to do. Pass it on as written.
- Some things are never returned, on purpose: medical notes, form answers, waiver documents, staff notes and emergency contacts. Say they are kept in Hurdle. Do not try another tool to find them.
- A program's billing type is fixed when it is created. If the person asks to switch one, say so and call `get_help` for the way around it.
- A request nobody approves expires, and nothing is sent or changed. Do not tell the person a waiting request is done.
- The same question asked in Hurdle's own Agents page gives the same numbers, because both run the same reports. If the person sees a difference, check the date range first.
