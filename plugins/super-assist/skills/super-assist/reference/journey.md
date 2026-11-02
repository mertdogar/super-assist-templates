# journey [goal]

Drafts journeys from the page catalog: each a goal and 1 to 10 steps, every step a page the catalog has and optionally one action or field that page lends. They are created as `manual` drafts; a member sets the trigger and publishes them.

## 1. Connect and read

Follow [connect.md](connect.md). In one `execute`, `GET /api/v1/projects/{id}/pages` and `GET /api/v1/projects/{id}/journeys`, and return both. When the catalog is empty, or the scan has pages it lacks, run [sync.md](sync.md) first.

## 2. Draft

With a goal, draft one journey toward it. Without one, propose up to three goals an end user of this app would come for, unlike the existing journeys, each with three or four steps, and let the user pick.

A journey reads as a storyboard:

- the first step introduces the page it starts on, with no element;
- each later step points at one action or field, on the page the previous step left the end user on or one it links to;
- a step's `path` and `element` come from the catalog: the page's `path`, and an element's `kind` and `name` from that page's `elements`;
- a `title` of up to 120 characters says what to do, a `body` of up to 1,000 says why, in the app's own words.

Show each draft's goal and its steps (path, element, title) before creating it.

## 3. Create

For each journey the user picked, `POST /api/v1/projects/{id}/journeys` with a `name` in lowercase letters, numbers and single hyphens, unique in the project, the `goal`, `trigger: "manual"` and the `steps`; all of them in one `execute`. A name already taken answers `409` `journey_name_taken`: pick another. Each answers `201` with the journey, its `state` `draft`: end users see nothing of it yet.

## Done

Done when every journey the user picked answered `201`. The hand-off names each draft, and says a member sets its trigger (`page` starts it once per end user on its first step's page) and publishes it on the project's **Journeys** page.
