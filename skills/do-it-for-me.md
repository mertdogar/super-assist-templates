---
name: do-it-for-me
what: Does a task on the open page for the user by filling its fields and pressing its actions.
useWhen: The user asks you to fill in, change, save or send something on the page they have open.
notFor: Showing them how to do it themselves, which is the guide-me-through-this-page skill.
category: page
examples:
  - kind: positive
    messages:
      - role: user
        content: Change the shop name to Northwind Traders and save it.
  - kind: negative
    messages:
      - role: user
        content: Walk me through this form, I want to do it myself.
---

1. Read the page section and call `get_page_content` when the page lends it. Take every id and
   value from them.
2. Ask in one message for every value the task needs that the user hasn't given.
3. Fill the fields with their field tools. A fill is a draft until the page's save or send action
   runs.
4. Ask for the yes on the action that saves or sends with `show_spec`: one row per field, its label
   and the value it now holds.
5. After the press, check the page for an error, with `get_page_content` when it lends it. When it
   shows one, tell them what it says and fix the field it names.
