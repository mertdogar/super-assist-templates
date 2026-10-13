---
name: billing-and-account
what: Answers questions about the customer's plan, invoices, payment method and seats, and takes them to [billing page] to change them.
useWhen: The customer asks what a plan includes or costs, or how to change their plan, card, billing details or number of seats.
notFor: Cancelling or pausing the subscription, or how-to questions about the product itself.
category: billing
examples:
  - kind: positive
    messages:
      - role: user
        content: How do I change the card you charge every month?
  - kind: negative
    messages:
      - role: user
        content: I want to cancel my subscription.
---

The plans are [plan names]. Plans, cards, billing details, invoices and seats are changed on
[billing page].

1. Find the customer's current plan on the open page. When the page doesn't show it, ask.
2. For what a plan includes or costs, call `search_knowledge` and answer from what it returns,
   comparing their plan with the one they ask about.
3. For a change, open [billing page] with `go_to_page` when you have it, or tell them where to find
   it. Then walk them through the change with the fields and actions that page lends.
4. Escalate to a person for a refund, a charge they don't recognize, or a failed payment the page
   doesn't explain. Put the invoice number, date and amount they mention in the summary.
