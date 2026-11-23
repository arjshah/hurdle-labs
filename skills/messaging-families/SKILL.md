---
name: messaging-families
description: >-
  Email, text, SMS, message, announcement, reminder, notify parents, tell families,
  draft a message, reply to the inbox. Prepares an email or text to one family, a
  program or a team and submits it for approval in Hurdle. Applies the rules that keep a
  business out of trouble: parents and guardians only, opt-outs respected, quiet hours
  for texts, no card details, balances only to the person who pays. Use when the person
  wants to send, draft or check a message to families or leads.
license: see LICENSE
metadata:
  version: "1.0.0"
  tools: "get_business_overview search_people list_people get_person list_registrations list_programs list_teams get_team get_inbox_summary get_request_status get_help list_sent_messages get_conversation"
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

Use this skill for any email or text to families, members or leads, and for questions about what is waiting in the inbox.

Other skills decide what a message is about. This one decides who may receive it, when and how:

- Payment reminders: collecting-payments.
- Schedule change notices: handling-schedule-changes.
- Lead and trial follow-up: following-up-leads-and-trials.
- Check-ins with families who may leave: keeping-families-enrolled.
- Re-enrollment announcements: planning-the-next-season.

## Ask before acting

- Who receives it: one family, a program, a team, or a list of people.
- Email or text.
- The points the message has to make. Draft the wording, then show it.
- For a text outside 9am to 8pm: whether the owner says it is urgent.
- Any fact not yet known: a date, a price, a place. Do not fill a gap with a guess.

## Steps

1. Call `get_business_overview` for the business's name, words and time zone, unless already known.
2. Build the audience from Hurdle, not from typed names:
   - One family: `search_people`, then `get_person` to find the parent or guardian.
   - Everyone in a program: `list_programs` to find it, then pass the program to the tool. Use `list_registrations` when only some registrants should receive it, and pass their ids.
   - A team: `list_teams` or `get_team`, then pass the team.
   - A wider group: `list_people` with a filter, then pass the ids.
3. Check the audience:
   - Recipients are parents and guardians. A child is not a recipient. An adult member receives their own messages.
   - Leave out anyone who opted out.
   - State the count from the tool's total, and name the group.
4. Draft the message. Keep it short. Put the action and the date first. Use the business's own words for classes and members.
5. Show the person the draft, the audience and the channel.
6. Call `send_message` with the ids, the program or the team. It waits for approval in Hurdle. The summary shows who it will go to.
7. Tell the person it is waiting under Agents → Approvals and that nothing has been sent.
8. If they ask later whether it went out, call `get_request_status` with the request id.
9. For "what needs a reply", call `get_inbox_summary`.

Before a second message to the same family, call `list_sent_messages` to see what they already received. To answer a family's message, call `get_conversation` to read the thread first, so the reply answers what they asked.

## Defaults

- Email for anything planned or detailed. Text for something that matters in the next few hours.
- Texts go out between 9am and 8pm in the business's time zone. A message is sent when it is approved, so if it is outside those hours, say so and suggest the person approves it in the morning.
- One message per subject. A second message goes out only with new information.
- Sign with the business's name.
- A draft the person has not seen is not sent for approval.

## Gotchas

- `send_message` never sends from the chat. Every message waits for approval in Hurdle. A message that was not approved was not sent.
- Recipients come from ids, a program or a team. A typed name is not a recipient. Two families can share a name, and a message to the wrong one cannot be recalled.
- A balance goes only to the person who pays it. In a split household, the other parent does not see it. Do not put one family's amount in a message to a group.
- Do not ask for card details in a message. Email and text are not safe places for card numbers. Families add a card on Hurdle's secure page.
- Do not write to a child, even a teenager with their own phone number on file, unless the owner says the business does this.
- Opt-outs apply to announcements and marketing. If a family opted out, leave them out and tell the person how many were skipped.
- A link in a message is one a tool returned. The business's homepage is not a sign-up or payment page.
- Do not put health, behaviour or family circumstances in a message to a group.
- Nothing here can attach a file.
- An approval nobody acts on expires and nothing is sent. Offer `create_task` if the person wants someone reminded to approve it.
