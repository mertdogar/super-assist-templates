---
name: troubleshooting
what: Works through a problem with the customer one fix at a time, checking the result of each before the next.
useWhen: Something fails or behaves unexpectedly for the customer, such as an error, a button that does nothing, missing data or a page that won't load.
notFor: How to use a feature that works as designed, or a problem with a payment.
category: support
examples:
  - kind: positive
    messages:
      - role: user
        content: I click Export and nothing downloads.
  - kind: negative
    messages:
      - role: user
        content: Can I export my reports to CSV?
---

1. Pin down the problem: what they did, what they expected and what happened instead, with any
   error message word for word. Read what the open page shows, such as an error or a field's value,
   and ask only for what it doesn't show.
2. Call `search_knowledge` with the error text or the symptom. From the passages, list the fixes,
   most likely and least effort first.
3. Give one fix, with what they should see when it works, and ask what they see now.
4. When it worked, confirm the original problem is gone and stop. When nothing changed, give the
   next fix. When something new happened, go back to step 1 with it.
5. When the fixes run out, escalate to a person. Write the summary as a bug report: the symptom,
   the error text, the page, and each fix tried with its result.
