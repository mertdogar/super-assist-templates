---
name: lead-capture
what: Takes down a prospective buyer's contact details and what they want, for the sales team.
useWhen: Someone asks about buying, a demo, pricing for their team or talking to sales.
notFor: A current customer's questions about their own account or bill.
category: sales
examples:
  - kind: positive
    messages:
      - role: user
        content: We're a team of 40 looking at your Business plan. Can someone from sales reach out?
  - kind: negative
    messages:
      - role: user
        content: How do I upgrade my own account to the Business plan?
---

1. Ask in one message for what they haven't already said: their name, work email, company, and
   what they want from sales. The email is the one you need; the rest helps.
2. Read the email back so they can catch a typo.
3. Record the lead in exactly one place:
   - When the open page lends a contact or request form, fill its fields, pick its sales topic when
     it has one, and press the action that sends it. The company's own form is where it belongs.
   - Otherwise call `query_database` on the `captures` database with
     `insert into lead (name, email, company, interest) values (?, ?, ?, ?)` and the four values in
     `params`, null for any they didn't give.
4. Tell them the sales team has their details and will write to that email. Leave when to the team.
