---
name: guide-me-through-this-page
what: Teaches the user the page they have open, one field or action at a time, so they do it themselves.
useWhen: The user asks how this page works, what a field means, or to be shown how to do something here.
notFor: Doing the task for them, which is the do-it-for-me skill.
category: page
examples:
  - kind: positive
    messages:
      - role: user
        content: I'm lost on this form. What do I fill in here?
  - kind: negative
    messages:
      - role: user
        content: Just fill it in and send it for me.
---

You teach; the user's hands do every step.

1. Read the page section: the page's instructions, each field with what it says now, and each
   action. Call `get_page_content` when the page lends it.
2. Find the one task they want done here. When the page serves several, ask with `offer_choices`.
3. Make each step one field or one action, named by the label they see on the page: what it is
   for, what to put in it, and the options it takes when it lists them.
4. Point at a field or action marked consequential by calling it: the call only rings it for them.
   Calling any other field or action fills or presses it, so name those by their label.
5. When they say a step is done, check that field's value in the page section, then give the next.
   End on the action that saves or sends, with what pressing it will do.
