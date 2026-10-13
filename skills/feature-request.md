---
name: feature-request
what: Records a customer's request for something the product doesn't do yet, for the product team.
useWhen: The customer asks for a feature, an integration or a change the product doesn't have.
notFor: Something the product already does that isn't working, or how to use it.
category: support
examples:
  - kind: positive
    messages:
      - role: user
        content: Can you add a way to export my orders to Xero?
  - kind: negative
    messages:
      - role: user
        content: The CSV export gives me an empty file.
---

1. Check that it is new. When the knowledge base shows the product already does it, show them how
   and stop here.
2. Ask what they're trying to get done and what it would save them. The problem behind a request
   is what the product team weighs. Ask for their email only if they want to hear when it ships.
3. Record the request in exactly one place:
   - When the open page lends a request or feedback form, fill its fields, pick its feature request
     topic when it has one, and press the action that sends it. The company's own form is where it
     belongs.
   - Otherwise call `query_database` on the `captures` database with
     `insert into featureRequest (request, reason, email) values (?, ?, ?)`: the request in their
     words, the problem it solves, and their email or null.
4. Tell them you passed it to the product team. Leave dates and decisions to that team.
