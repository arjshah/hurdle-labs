---
name: managing-enrollments
description: >-
  Enroll, register, add to a class, move, transfer, switch class or price, cancel,
  withdraw, pause, freeze, resume, waitlist, open spots. Handles sign-ups for a person
  the business already knows: enrolling with a complimentary spot, a pay link or a
  payment already received in person, moving at the price already paid, cancelling
  without a refund, pausing and resuming a membership, and reading waitlists and open
  spots. Use when the person wants to change who is signed up for what. Card charges and
  refunds happen in Hurdle.
license: see LICENSE
metadata:
  version: "1.0.0"
  tools: "get_business_overview search_people get_person list_programs get_program list_registrations list_memberships get_availability get_help"
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

Use this skill when the person wants to enroll someone, move them to another program, class time or price, cancel a registration, pause or resume a membership, or see a waitlist or open spots.

It does not cover:

- Adding someone to a single session as a trial, drop-in or make-up. Use running-daily-operations.
- A brand new enquiry who is not yet a member. Use following-up-leads-and-trials.
- Creating or changing the program itself. Use planning-the-next-season.
- A family thinking of leaving. Use keeping-families-enrolled before cancelling.

## Ask before acting

- Which person and which program. Confirm both by name before any change.
- Which price option, when the program has more than one.
- For an enrollment: how the family pays. The three choices are below.
- For a move: the destination program and price option, and class time if the program has several.
- For a cancellation: when it takes effect, and whether a refund is due. A refund is issued in Hurdle.
- For a pause: the resume date.

## Steps

### Enroll

1. Find the person with `search_people` and the program with `list_programs`.
2. Call `get_program` to read its price options, capacity and open spots.
3. Call `enroll_person` with one payment choice:
   - "complimentary": the spot is free. Use it only when the person says so.
   - "send-link": Hurdle sends the family a pay link. The registration stays pending until they pay.
   - "offline": the family already paid in person, by cash or check, and this records it.
4. Show the preview summary, wait for a yes, then confirm.
5. Tell the person the status the tool returned. Pending is not enrolled.

### Move

1. Call `get_person` to see the current registration, and `get_program` for the destination.
2. Check what "move" means. "Move to U10" may be a price option inside the same program, not a team or a different program. Read the program's price options before choosing.
3. Call `move_registration`. The person moves at the price they already pay. Nothing is charged and nothing is refunded.
4. If the two prices differ, say so with both figures from the tools. The difference is settled in Hurdle.
5. Show the preview, wait for a yes, then confirm.

### Cancel

1. Call `get_person` to find the registration and its billing.
2. Call `cancel_registration`. It cancels with no refund.
3. Show the preview, wait for a yes, then confirm.
4. If a refund is due, say it is issued in Hurdle. Offer `create_task` so it is not forgotten.

### Pause or resume

1. Call `list_memberships` or `get_person` to find the membership and its status.
2. Call `pause_membership` with the resume date, or `resume_membership`.
3. Show the preview, wait for a yes, then confirm. State the date billing restarts.

### Waitlists and open spots

1. Call `get_program` for capacity, open spots and the waitlist count.
2. Call `list_registrations` for the program to see who is waitlisted, in order, and who is unpaid.
3. For appointment programs, call `get_availability` for open slots.
4. Admitting someone from a waitlist can charge their saved card, so it is done in Hurdle. Tell the person who is next in line and call `get_help` for the steps.

## Defaults

- If the person says only "enroll them", use the pay link. It gives nothing away for free and records no money that was not received.
- When the tool offers a choice of when a subscription ends, use the end of the billing period. The family keeps what they paid for and no refund is needed.
- A pause has a resume date. Ask for one. An open-ended pause is easy to forget and the family stops paying without anyone deciding that.
- Waitlists are worked in order. Nobody skips the line unless the person says so.

## Gotchas

- A card charge is never an option here. If the person says "put it on their card", say that is done in Hurdle, and offer the pay link.
- "Paid in person" fits a one-time price. A subscription or a payment plan needs a saved card, so send a pay link for those. If the tool refuses, pass on its reason.
- A pay link for a monthly program saves a card. The first charge follows the program's billing schedule. Do not tell the family they will be charged the moment they click.
- A move works only between options that bill the same way: one-time to one-time, monthly to monthly, plan to plan. Otherwise it is a cancellation and a new enrollment, which involves money and is done in Hurdle.
- A waitlisted or unpaid sign-up does not hold a seat. Do not count it as a filled spot.
- Cancelling takes the person off the rosters tied to that registration. Say this before the person confirms.
- A payment plan and a one-time registration cannot be paused. A program can also set a longest pause.
- A class can fill between the preview and the confirmation. If the confirmed call is refused, report the reason. Do not retry with different input.
- A changed input needs a fresh preview. The confirmation works only with the same input the person saw.
