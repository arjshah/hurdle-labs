# Hurdle for Claude and ChatGPT

Run a youth sports club or an activity business from your assistant. Ask about members, classes, payments, and the schedule in plain words, and make everyday changes without opening Hurdle.

## Who it is for

Owners and admins of a club, gym, studio, or school on Hurdle. Instructors and families cannot connect. You need a Hurdle account.

## What it does

The plugin connects your assistant to Hurdle and adds ten playbooks, so the assistant handles each job the way a careful operator would:

- **working-in-hurdle**: how Hurdle is organised and where to find things.
- **running-daily-operations**: the morning check and front-desk jobs.
- **collecting-payments**: who owes money and how to follow up.
- **managing-enrollments**: enroll, move, cancel, pause and waitlists.
- **handling-schedule-changes**: closures, cancelled sessions and substitutes.
- **following-up-leads-and-trials**: new enquiries and trial classes.
- **keeping-families-enrolled**: spotting families who may leave.
- **messaging-families**: emails and texts, with the rules that keep you safe.
- **reviewing-business-performance**: weekly and monthly reviews.
- **planning-the-next-season**: re-enrollment and setting up new programs.

## Where your data goes

- Your assistant sends your request to Hurdle's server at https://gohurdle.com/api/mcp.
- Hurdle returns your business's own data to the assistant. It never returns another business's data.
- In ChatGPT, details of children under 13 are never returned. A child appears as a household, such as "a child in the Lee household".
- Medical notes, form answers, waiver documents and staff notes are never returned, in any assistant.
- No card can be charged. Refunds, collecting a missed payment and changing a card on file are also not possible from the assistant. You do these in Hurdle.
- Hurdle keeps a record of each request made through the assistant: who asked, what was asked for and the outcome.

## How changes are confirmed

Reading is always safe. Changes are confirmed in one of three ways:

1. **Small additions** such as a new lead, a note or a task are saved after your assistant asks you.
2. **Changes** such as enrolling a member or moving a class are previewed first. You see a summary of exactly what will happen, and nothing is saved until you say yes.
3. **Anything that reaches many families**, such as a message or a closure, waits in Hurdle under **Agents → Approvals**. Nothing is sent or changed until an owner or admin approves it there.

## How to disconnect

- Remove the Hurdle connector in your assistant's settings.
- An owner can turn the connection off for the whole business in Hurdle under **Agents → Settings**.

## Support

Email support@gohurdle.com.

## License

See [LICENSE](LICENSE).
