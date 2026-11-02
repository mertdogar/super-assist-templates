# sync [dir]

Makes the project's page catalog match the app's code: scan, correct `destinations` in the code, fill every gap the scan couldn't read, post the whole catalog with `replace: true`, then ask about the journeys whose steps name what's gone. `dir` is the app's directory, as the Stop hook's nudge passes it in a repository of several apps.

## 1. Connect and find the project

Follow [connect.md](connect.md) for the app in `dir`, or the app the scan finds: the `execute` tool and the project's `id`.

## 2. Scan

Run `node <skill dir>/scripts/super-assist.mjs scan [dir] --json` and keep its output (see **The scan** in SKILL.md).

## 3. Correct `destinations` in the code

At `destinations.file` and `destinations.line`, following **Build `destinations` from the routes** in SKILL.md:

- each entry of `missing` gets a key for its `path`;
- each entry of `wrong` moves to the path its page has now when the route was renamed or moved (`git diff` shows which), and goes otherwise.

Scan again. Done when `missing` and `wrong` are empty.

## 4. Fill every gap in `dynamic`

Each gap names a `file`, `line` and `source`. Read the code there and write into `body.pages` what the page shows at runtime:

| `what`           | The gap                                        | Fill                                                                                                     |
| ---------------- | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| `name`           | the page's `name` arrives as `""`              | the name a member would know it by, with the variable part dropped: `` `Order #${id}` `` becomes `Order` |
| `instructions`   | the page has no `instructions`                 | the instructions in a few sentences, from what the expression says in every case                         |
| `action`/`field` | an element whose name is computed is left out  | an element of that `kind` for each name it can take, `byId: true` when rows lend it, else with a `label` |
| `label`          | the `element`'s label is computed and left out | a `label` saying what pressing or filling it does                                                        |
| `path`           | a route `go_to_page` can't reach is left out   | nothing: it stays out of the catalog, and the hand-off names it                                          |

Done when no page in `body` has an empty `name` and every gap is filled or, for a `path`, named for the hand-off.

## 5. Post

The post takes 1 to 100 pages and at most 2,000 elements. When `body` holds more, stop and tell the user: a replace needs the whole catalog in one post.

In one `execute`:

1. `GET /api/v1/projects/{id}/pages`, the catalog before;
2. `POST /api/v1/projects/{id}/pages` with `body`, which answers the catalog after;
3. `GET /api/v1/projects/{id}/journeys`;

and return all three. `replace: true` removes the pages left out with their elements, and the elements each page sent leaves out. A page that really lends something sync dropped reports it back the next time an end user opens it.

## 6. Journeys that name what's gone

A step names what's gone when the catalog after has no page at its `path`, or that page doesn't lend its `element` (`kind` and `name`). Check each journey's `steps`, and its `publishedSteps` when it has them.

Ask about every affected step in one message, the turn's last:

- **Renamed or moved**, where the before and after catalogs, or `git diff`, show what took its place: "Step 2 of _First refund_ points at `refund` on `/orders/:id`, which is now `issue-refund`. Retarget it?"
- **Deleted**, with nothing in its place: "Drop the step, or leave it for you to fix on the Journeys page?" A step is dropped only on the user's word, and a journey keeps at least one step.

On the answers, `PATCH /api/v1/projects/{id}/journeys/{journeyId}` with the whole changed `steps` of each journey the user said yes for. A journey that has `publishedSteps` shows end users the old ones until it is published again: ask "Publish it now?" and on a yes `POST /api/v1/projects/{id}/journeys/{journeyId}/publish`. On the curl path the publish is refused, so say to publish it on the **Journeys** page.

## Done

Done when the post answered 2xx and every affected step is asked about. The hand-off, in the same message as the questions, lists the pages added, changed and removed, the `destinations` edits, the gaps filled, any route left out, and the journeys the questions cover.
