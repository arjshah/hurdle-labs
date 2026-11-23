---
name: planning-the-next-season
description: >-
  Next season, next session, next term, re-enrollment, re-registration, returning
  families, priority registration, new program, new class, add a class time, set prices.
  Plans the handover from one season to the next: when to open re-enrollment, a priority
  window for current families, which class times to keep or drop based on fill rate
  against the business's own prior season, and setting up programs, prices and class
  times with a preview before anything is saved. Use when the person is planning or
  building what comes next.
license: see LICENSE
metadata:
  version: "1.0.0"
  tools: "get_business_overview list_programs get_program get_program_attendance_breakdown get_report get_availability list_registrations list_closures list_staff list_venues get_help"
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

Use this skill when the person is planning the next season, session or term: when to open sign-ups, what to offer, what to change, and setting up the programs.

It does not cover:

- Moving or enrolling one family into the new season. Use managing-enrollments.
- A change to this season's calendar. Use handling-schedule-changes.
- A review of numbers with no planning decision. Use reviewing-business-performance.

## Ask before acting

- When the current season ends and when the next one starts and ends.
- Which programs carry over, which are new and which stop.
- Every price. Do not carry a price over or round one without asking.
- Class days, times, venue and coach for each program.
- Capacity for each program, and whether it has a waitlist.
- How long current families get before sign-ups open to everyone.
- Days the business is closed during the season.

## Steps

### Review this season

1. Call `get_business_overview` for the business's words and time zone, unless already known.
2. Call `list_programs` for the running programs, then `get_program` on each for capacity, registrations, waitlist and prices.
3. Work out the fill rate from the tool's figures: registered against capacity. Use `get_program_attendance_breakdown` to see which class times were full and which were thin.
4. Call `get_report` with membership and attendance for the season, and for the prior season.
5. Judge each class time against the business's own prior season:
   - Full with a waitlist: a candidate for a second class time.
   - Fill rate well below the prior season: a candidate to move or drop. Ask the person why it was thin before suggesting a cut.
   - Steady: keep.
6. For appointment programs, call `get_availability` to see how much open time went unbooked.

### Plan the timeline

1. Count back from the end of the current season. Re-enrollment starts 6 to 8 weeks before the session ends.
2. Current families get a priority window before sign-ups open to everyone.
3. How the window is enforced depends on the program's settings. Call `get_help` with "invite-only program" or "registration window" and tell the person what Hurdle supports.
4. Offer `create_task` for each date on the timeline: announce, open to current families, open to all, last reminder.

### Set up the programs

1. Read the whole plan back to the person first: name, dates, class times, venue, coach, prices, capacity.
2. Call `list_venues` and `list_staff` to match the names the person gave. Call `list_closures` to find closed days inside the season.
3. Call `create_program` for each new program. Show the preview summary, wait for a yes, then confirm.
4. Call `add_price` for each price option, and `add_class_time` for each weekly class time. Each one is previewed and confirmed on its own.
5. Call `update_program` to change details of an existing program, such as dates or capacity.
6. After each confirmed call, report what the tool returned. A program can be saved as a draft. Say so, and say what is still needed before families can sign up.

### Announce

1. Call `list_registrations` for the current season to get the families who are enrolled now.
2. Draft the re-enrollment message: what is offered, when their window opens and closes, and the sign-up link a tool returned.
3. Send with `send_message` to the program or to the ids. It waits for approval in Hurdle.

## Defaults

- Start re-enrollment 6 to 8 weeks before the session ends. Earlier, and families have not decided. Later, and they have made other plans.
- Current families go first. They have earned the spot, and it keeps this season's classes together.
- A weak class time is judged by fill rate against the business's own prior season, not against another business.
- Keep a price as it is unless the person gives a new one.
- New programs are built one at a time, so each preview can be read properly.

## Gotchas

- Some choices are fixed when a program is created: how it bills (one-time, monthly or payment plan) and whether it has a registration window or is open all the time. Confirm these before `create_program`. Changing them later means a new program.
- A price change reaches new sign-ups only. Families already enrolled keep the price they have.
- A price that families have bought can be hidden from new sign-ups but not removed.
- A free price next to a paid one is open to anyone. For a discount for some families, the business uses discount codes in Hurdle.
- A program needs a price and a schedule before it can be published.
- A new class time starts on the next matching weekday unless a first date is given. State the first date.
- The preview can point out past dates, a room or coach already booked, and closed days. Read these to the person. Do not confirm past them without a yes.
- A new program starts with an empty waitlist. Use this season's waitlist to see demand, and invite those families early if the owner agrees.
- Do not drop a class time because of one thin season. Compare it with the prior season first, and ask whether something else explains it, such as a new coach or a changed time.
- Announcing before the program can take sign-ups sends families to a page that does not work. Check with `get_program` first.
