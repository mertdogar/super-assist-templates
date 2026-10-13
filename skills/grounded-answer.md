---
name: grounded-answer
what: Answers a product question from the passages search_knowledge returns, and says so plainly when they don't cover it.
useWhen: The customer asks how something works, how to do something, or whether the product can do something.
notFor: Something failing for the customer, or questions about their own plan, invoices or payment.
category: support
examples:
  - kind: positive
    messages:
      - role: user
        content: Can I export my reports to CSV?
  - kind: negative
    messages:
      - role: user
        content: I click Export and nothing downloads.
---

1. Call `search_knowledge` with the question in the product's words: the feature, setting or page
   the customer means, plus any term or error they used.
2. Keep the passages that are on point: a passage is on point when it covers the feature or task
   asked about, not when it only shares words with the question. When none is on point, search
   once more with other words: a synonym, the name the open page uses, or the broader task.
3. Answer from the passages on point: their steps in their order, with the exact names of the
   buttons, settings and limits they give. The panel lists the sources under your answer, so link a
   `url` only to send the customer to read that page, and only a `url` a passage carries.
4. When passages disagree, give both answers and name each one's source title.
5. When they cover only part of the question, answer that part and say which part the help content
   doesn't cover.
6. When nothing is on point after the second search, say the help content doesn't cover it and
   offer to escalate to a person.
