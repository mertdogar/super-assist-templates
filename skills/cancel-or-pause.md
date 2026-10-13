---
name: cancel-or-pause
what: Makes one retention offer to a customer who wants to cancel, then cancels or pauses with the page's action or hands off to a person.
useWhen: The customer asks to cancel, pause or stop paying for their subscription, or to close their account.
notFor: Moving to another plan, or cancelling something inside the product such as an invite, an order or a scheduled job.
category: billing
examples:
  - kind: positive
    messages:
      - role: user
        content: I'd like to cancel, we're not using it enough to justify the cost.
  - kind: negative
    messages:
      - role: user
        content: How do I cancel an invite I sent to the wrong email?
---

When they ask to pause, start at step 3.

1. Ask what makes them want to cancel, in one question.
2. Make one offer that fits their reason: a fix from `search_knowledge` for a problem they name, a
   pause instead, or [retention offer]. A no, or no reason given, ends the offer; go on with what
   they asked for.
3. Carry out the cancel or pause:
   - When the open page lends a cancel or pause action, use it.
   - Otherwise open [billing page] with `go_to_page` and use its action there. When you can't open
     pages, tell them where [billing page] is and which control to use.
   - When there is no action to use, escalate to a person, with what they chose and why in the
     summary.
4. Tell them when the change takes effect and what happens to their data, as the page or the
   knowledge base states it.
