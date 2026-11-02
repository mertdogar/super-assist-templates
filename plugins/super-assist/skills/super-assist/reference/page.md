# page [route]

Makes one route's page assisted: an `AssistedPage` with its name, instructions and content, and the actions, fields and tools the assistant should use there.

## 1. Pick the route

Take the route the user named. Without one, compare the app's routes with the pages the scan lists, and offer the routes that render no `AssistedPage`, the ones end users spend their time on first.

## 2. Read the page and the docs

Read the route's page and the components it renders. Fetch `https://superassist.app/how-to/make-a-page-assisted.md`, plus the docs page of each piece the page needs from the **Docs** table in SKILL.md: actions, form fields, tools, consequential.

## 3. Lend what the page has

Follow **Lend what a page has** in SKILL.md:

- `AssistedPage` with a literal `name`, `instructions` naming its actions and tools and which action saves, and `content` returning the ids and values the assistant acts on;
- `AssistedAction` around each button the assistant should press, rows sharing one `name` with their own `id` and `label`;
- `AssistedInput` or `AssistedField` around each field it should fill;
- `useAssistedTool` for what has no element to press;
- `consequential` on paying, refunding, deleting and sending.

Add the route to `destinations` when it is missing.

## 4. Check it

Run the scan. Done when the route's page is in `body` with its name and every element you lent, `destinations.missing` and `destinations.wrong` are empty, and `dynamic` holds nothing for this path that a literal could have said. Then [Verify](../SKILL.md#verify) in the running app when the user can open it.

The hand-off names `sync` as the next step, so the page catalog carries the page; in Claude Code, the Stop hook asks for it once the scan changes.
