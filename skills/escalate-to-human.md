---
name: escalate-to-human
what: Hands the thread to a person at the company with the handoff tool, with a summary they can act on.
useWhen: The customer asks for a person, or the knowledge base and the page leave their problem unsolved.
notFor: A question you can still answer from the knowledge base or the page, or a request to talk to sales.
category: support
examples:
  - kind: positive
    messages:
      - role: user
        content: My invoices show our old company name and I can't edit it.
      - role: assistant
        content: The billing page has no field for the name on past invoices, and the help content doesn't cover changing it.
      - role: user
        content: Then can someone from your team fix it?
  - kind: negative
    messages:
      - role: user
        content: How do I invite a person from my team to the workspace?
---

1. Make sure you know what the customer needs. When the thread doesn't show it yet, ask in one
   question.
2. In one message, tell them a person from the company will read this conversation and reply here
   during [support hours], and ask for an email address in case they leave before the reply. A yes
   without an address is enough.
3. Call `handoff`:
   - `reason`: what the person must do, in one line, for example "Correct the company name on past
     invoices".
   - `summary`: what the customer needs, what you tried and found, and every detail they gave, such
     as an account email, invoice number or error text, so the person can act without asking again.
   - `contact`: the address they gave, or leave it out.
4. Pass on the tool's answer and end your turn.
