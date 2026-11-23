---
name: collecting-payments
description: >-
  Failed payments, declined or expired cards, overdue balances, who owes money, late
  payment reminders. Sorts each unpaid item by cause (hard decline or expired card, soft
  decline, no card on file, payment plan behind, bank payment still clearing), leaves
  out families who should not be chased, and runs a follow-up sequence of text, then
  email, then a staff task. Use when the person asks who owes money, why a payment
  failed, or wants to chase unpaid balances. Money itself is collected in Hurdle, not
  here.
license: see LICENSE
metadata:
  version: "1.0.0"
  tools: "get_business_overview get_money_owed list_payments check_cards_on_file get_person search_people get_help"
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

Use this skill when the person asks who owes money, why a payment failed, which families have no card, or wants to follow up on unpaid balances.

It does not cover:

- Revenue trends and totals over time. Use reviewing-business-performance.
- Messages that are not about money. Use messaging-families.
- A family that wants to pause or cancel because of cost. Use managing-enrollments.

## Ask before acting

- The business's rules on late payments: any grace period, late fee, and the point at which the owner wants to be told. If there are none, use the Defaults.
- Whether any family on the list has a scholarship, a private arrangement, paid outside Hurdle, or is disputing the charge. The tools may not show these.
- Before any message goes for approval: who receives it and whether it is a text or an email.

## Steps

1. Call `get_business_overview` for the business's words, time zone and currency, unless already known.
2. Call `get_money_owed`. Each item shows how late it is, the last attempt and why it failed, whether there is a card on file, and when the family was last contacted. Quote the totals the tool returns.
3. Put each item into one case. Use the failure reason the tool gives. If there is no reason, say so and do not guess one.
   - **Bank payment still clearing.** The money is on its way. It is not failed. Leave it alone.
   - **Soft decline**, such as not enough funds, or an item not yet late. The payment processor retries on its own schedule. A message is not needed while it is retrying.
   - **Hard decline or expired card.** A retry cannot work. The family needs to add a new card. Ask them to update it through Hurdle's secure page.
   - **No card on file.** Call `check_cards_on_file` to confirm. A parent's saved card counts for their child.
   - **Payment plan behind.** An installment failed. Hurdle emails the family a button to update their payment method, and the family is still registered.
4. Set aside the families who should not be chased:
   - A family whose payment plan is up to date. Future installments are not late.
   - A scholarship family, or one marked as paid.
   - A family disputing the payment. A reminder during a dispute makes it worse. Create a task for the owner.
5. For each remaining family, call `get_person` to find who pays. Use `search_people` when the person names a family that is not on the list. A balance is discussed only with the parent or guardian the payment bills.
6. Use `list_payments` when it helps to check whether a payment has come through since, or to see a family's recent history.
7. Pick the step from how late the item is and when the family was last contacted:
   - Day 1: a short, friendly text.
   - Day 3: an email with the amount, what it is for, and how to pay or update the card.
   - Day 7: a staff task to call the family. Use `create_task`.
8. Send with `send_message`, using the payer's id from step 5. It waits for approval in Hurdle. Tell the person nothing has been sent yet.
9. Report by case: how many families, the amounts as the tool gave them, what is waiting for approval, which tasks were created, and what has to be done in Hurdle.
10. To take the money, the person works in Hurdle. Call `get_help` with "collect what a family owes" and give the steps it returns.

## Defaults

- Sequence: text on day 1, email on day 3, staff task on day 7, counted from the day the payment failed or fell due.
- One reminder per step. If the family was already contacted at this step, move on to the next step when it is due. Do not send a second copy.
- Tone: assume a card problem, not unwillingness to pay. The family may not know the card failed.
- Texts go out between 9am and 8pm in the business's time zone. Outside those hours, say so and suggest the person approves it in the morning.

## Gotchas

- Late fees, suspension and removal from a class are the owner's decision. Create a task for the owner with `create_task`. Do not send a family a message about any of these, even as a warning.
- Do not ask a family for card details in a message. Email and text are not safe places for card numbers. The family adds a card on Hurdle's secure page.
- Nothing here can charge a card, retry a payment, record cash or change the card on file. Saying "I've collected it" would be false. Say what was sent for approval and what remains to do in Hurdle.
- A family behind on a payment plan is still registered and keeps their spot. Do not describe them as removed.
- Hurdle already emails families when a payment fails. Check the last contact before adding another message, so a family does not get two on the same day.
- An online checkout that someone started and left is not money owed. It is a lead. Use following-up-leads-and-trials.
- A message that needs a payment link uses a link a tool returned. Do not write a link from memory or use the business's homepage.
- A missed payment can be paid only by the parent it bills. If that parent has no card, another parent's card does not fix it.
- Send a balance to one family at a time. A group message with amounts in it shows one family's balance to another.
