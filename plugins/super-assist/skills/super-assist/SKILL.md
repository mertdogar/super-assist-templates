---
name: super-assist
description: Builds with @super-assist/react, the assistant beside every page of a React app, and keeps the app's Super Assist project in step with its code. Use when code imports @super-assist/react; when the user wants an in-app assistant that reads a page, presses its buttons, fills its forms or runs its tools; or to set up Super Assist, sync its page catalog, draft journeys, sign its end users' identity or check an app's setup.
argument-hint: "[setup | sync [dir] | page [route] | journey [goal] | identity | doctor]"
---

# super-assist

`@super-assist/react` puts one assistant beside every page of a React app, on assistant-ui's model context. Pages introduce themselves and **lend** the assistant what it may use. The package ships a ready-made panel, `AssistedPanel`, with its parts and compiled styles (`@super-assist/react/styles.css`). `useAssistedRuntime`, from the same import, connects the panel to a project on Super Assist (superassist.app) with the project's publishable key; the project's agent answers, so the app writes no backend.

## Language

Name things in these words. The docs, the props and the model's text use them.

- **Assisted page**: a page, or a dialog over it, wrapped in `AssistedPage` with a name, instructions and content read on demand. The last one mounted is the open page.
- **Lend**: what a mounted component gives the assistant while it is shown. Lent things leave when it unmounts.
- **Assisted action**: an element the assistant presses, lent with `AssistedAction`.
- **Assisted field**: a field the assistant fills as a user types, lent with `AssistedInput` or `AssistedField`. A fill is a draft until the page's own save action runs.
- **Tool**: a capability with no element to press, run in the browser, lent with `useAssistedTool`.
- **Consequential**: an effect the user owns. The assistant **rings** it (points at it for the user to press) or asks in the panel first.
- **Panel**: where the conversation shows: floating, as a sheet, or embedded.
- **Project**: the app on Super Assist, with its publishable key (`pk_…`), allowed origins and default **agent**, the organization's named assistant.
- **Page catalog**: the project's record of the app's assisted pages, each with its path, name, instructions and the actions and fields it lends. **Sync** makes it match the code.
- **Journey**: steps that walk end users through the app toward a goal, each a page by its path and optionally one action or field it lends. Drafted by you, shown to end users once a member publishes it.
- **End user**: a person using the app, passed as `user`. **Verified** when the app's server signs who they are with the project's **identity secret**, **unverified** when the app passes them as they are, or a **visitor** when it passes no one.

## Docs

Every docs page is Markdown at its URL with `.md` appended, and https://superassist.app/llms.txt indexes them all. Fetch the page for your branch before writing its code: props, the exact text the model receives, and worked examples live there. Without web access, read the types in `node_modules/@super-assist/react/dist/index.d.mts`.

Paths below are under `https://superassist.app/`.

| Branch                                                                 | Read                                                                                                                                               |
| ---------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| An app with no `AssistedProvider` yet                                  | `start.md`, then `tutorials/add-an-assistant.md`                                                                                                   |
| The key, the signed-in user, a refused connection                      | `how-to/use-the-hosted-runtime.md`, `how-to/identify-end-users.md`, `reference/react.md`                                                           |
| The API's routes and bodies, with an organization key and curl         | `how-to/set-up-through-the-api.md`                                                                                                                 |
| What Super Assist does with a turn                                     | `explanation/how-a-turn-is-answered.md`                                                                                                            |
| A person taking over, the resolve question, or sources under an answer | `dashboard/handoffs.md`, `dashboard/knowledge-bases.md`, `components/panel/conversation.md`; nothing to build, the panel decides when to show them |
| The panel and its modes                                                | `how-to/place-the-panel.md`, `components/panel/roots-and-window.md`                                                                                |
| Changing the panel's parts                                             | `how-to/rearrange-the-panel.md`, `how-to/make-your-own-part.md`                                                                                    |
| Cards and structured answers                                           | `how-to/swap-a-card.md`, `how-to/show-structured-answers.md`                                                                                       |
| Styles                                                                 | `reference/styles.md`                                                                                                                              |
| A page or dialog                                                       | `how-to/make-a-page-assisted.md`                                                                                                                   |
| Buttons and row actions                                                | `how-to/add-actions.md`                                                                                                                            |
| Form fields                                                            | `how-to/lend-form-fields.md`                                                                                                                       |
| A tool                                                                 | `how-to/lend-a-tool.md`                                                                                                                            |
| Something the user should own                                          | `how-to/make-an-action-consequential.md`                                                                                                           |
| Opening other pages                                                    | `how-to/add-go-to-page.md`                                                                                                                         |
| Several threads                                                        | `components/panel/threads.md`                                                                                                                      |
| The exact model text and tool answers                                  | `reference/model-context.md`                                                                                                                       |

## Lend what a page has

Pick the piece by what the assistant should do:

| The assistant should                                     | Lend with                                           |
| -------------------------------------------------------- | --------------------------------------------------- |
| Know which page is open and read what it shows           | `AssistedPage` with `content={() => data}`          |
| Press a button or link                                   | `AssistedAction` around the element                 |
| Fill an input, textarea, native select, checkbox, switch | `AssistedInput` around the element                  |
| Fill a control that keeps its value in state             | `AssistedField` with `value` and `onFill`           |
| Filter, read or change state with no element to press    | `useAssistedTool`                                   |
| Open another page                                        | `destinations` and `navigate` on `AssistedProvider` |

Rules every page follows:

- `content` returns plain data carrying the ids and values the assistant acts on. It is read only when the assistant calls `get_page_content`.
- `instructions` name the page's actions and tools in a few sentences, and say which action saves a form.
- Rows share one `name`, each with its own `id` and a `label` saying what pressing that row does. A table of fifty rows lends one tool.
- Position is scope. A hook under `AssistedPage` belongs to that page; outside every page it is global. A page with tools is two components: state and `AssistedPage` above, the hooks in a child below.
- Actions and fields render inside an `AssistedPage`, and the wrapped child forwards its `ref` to the DOM element.
- Mark consequential what the user should own: paying, refunding, deleting, sending. `consequential` rings the element; `consequential="confirm"` asks in the panel, then presses. On a tool, `consequential: true` asks before `execute` runs. `trace` on `AssistedProvider` rings every press and fill as it happens, for the user to follow.
- A dialog is an assisted page of its own: `AssistedPage` inside the dialog's content, and the dialog non-modal so the panel stays usable.
- A tool that cannot do its job throws; the model receives `{ error }` and the turn goes on. Zod schemas and `render` components live at module scope.
- A tool the model should see an image from sets `toModelOutput`, returning `text` parts and `file` parts with base64 `data` and a `mediaType`; up to 4 files of 5 MB.
- Names, instructions and labels are string literals where you can, so the scan reads them and the page catalog carries them.

## Build `destinations` from the routes

Don't ask the user for the list of pages. Read it from the app's router, and write `destinations` at module scope next to `AssistedProvider`:

- One entry per route whose page renders an `AssistedPage`. A dialog has no route and no entry.
- The key is a short lowercase name for the page, the value is the route's path. In the Next.js app router, `app/page.tsx` is `home: "/"` and `app/orders/[id]/page.tsx` is `order: "/orders/:id"`; route groups `(name)` and slots `@name` add nothing to the path.
- A dynamic segment is written `:id` whatever the route calls it, and `go_to_page` fills only one. Leave out a route with two dynamic segments, or a catch-all.

## The scan

`<skill dir>` is the directory that contains this SKILL.md. From the user's repository, `node <skill dir>/scripts/super-assist.mjs scan [app dir] --json` reads the app's routes (Next.js app router, TanStack Router file routes) and its Assisted components, with Node and nothing to install. It prints:

- `app`: the app's directory, relative to where you ran it.
- `body`: the exact body of the page catalog post, `{ replace: true, pages }`.
- `destinations`: the file and line of `destinations`, with `missing` (each page `path` it lacks, with the route's `file`) and `wrong` (each `key` and `path` no route has).
- `dynamic`: what the scan couldn't read statically, each gap with its `path`, `what` (`path`, `name`, `instructions`, `action`, `field` or `label`), the `element` for a label, and the `file`, `line` and `source` to read.

Without `--json` it prints a summary for the user. With no app dir in a repository of several apps, it lists them and exits 1: take the app the user named, else ask which.

## Commands

| Command          | What it does                                                                                                   | Reference                                      |
| ---------------- | -------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
| `setup`          | Adds the panel to the app, reuses or creates its agent and project, writes the key, syncs, proposes journeys   | [reference/setup.md](reference/setup.md)       |
| `sync [dir]`     | Makes the page catalog match the code, corrects `destinations`, and asks about journeys that name what's gone  | [reference/sync.md](reference/sync.md)         |
| `page [route]`   | Makes a route's page assisted: its name, instructions, content, actions and fields                             | [reference/page.md](reference/page.md)         |
| `journey [goal]` | Drafts journeys from the page catalog, as `manual` drafts a member publishes                                   | [reference/journey.md](reference/journey.md)   |
| `identity`       | Signs the signed-in user: the identity secret in the server's env file, a token route, the runtime fetching it | [reference/identity.md](reference/identity.md) |
| `doctor`         | Checks the library, the key, the connection, the project, catalog drift and journeys naming what's gone        | [reference/doctor.md](reference/doctor.md)     |

Routing:

- **No argument:** read [reference/routing.md](reference/routing.md) and present its menu. Run a command only once the user picks it.
- **A command named or clearly implied**, as the plugin's Stop hook names `sync`: load its reference and follow it. Ask once if two commands fit.
- **Otherwise:** the request is building with the library. Follow the guide above, then [Verify](#verify).

`setup`, `sync`, `journey`, `identity` and `doctor` call Super Assist; each starts from [reference/connect.md](reference/connect.md).

## Verify

Install `@assistant-ui/react-devtools` and render `AssistedDevtools` from `@super-assist/react/devtools` inside the provider. Open its **Assisted pages** tab on the page you lent. Done when the tab shows the page's note, `get_page_content`'s answer, and every action and field you lent with the ids and values you expect; then ask the assistant in the panel to use one of them and watch the page change.
