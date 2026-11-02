# setup

Takes an app from nothing to a project on Super Assist: the panel in the code, an agent, a project with its key in the env file, a synced page catalog, and journeys drafted.

## 1. The provider

When the app already renders `AssistedProvider`, go on to step 2. Otherwise fetch `https://superassist.app/start.md` and `https://superassist.app/tutorials/add-an-assistant.md` and follow them: install `@super-assist/react`, render `AssistedProvider` with `useAssistedRuntime({ key })` reading the app's public env variable, `destinations` built from the routes, and the panel.

When the scan finds no assisted pages, offer to make the app's main routes assisted with `page` before going on: the page catalog takes at least one page.

## 2. Connect

Follow [connect.md](connect.md) until the `execute` tool is there, or the user picked the curl path.

## 3. The agent

When the env file already holds a key whose project [connect.md](connect.md) finds, keep that project and its agent, and go on to step 5.

Otherwise reuse, install or create, in that order:

1. The organization's agent named after the app, from `GET /api/v1/agents?limit=100`.
2. Else the template that fits the app, from `GET /api/v1/templates`, installed with `POST /api/v1/templates/{id}/install`. The install answers `201` with the agent named after the template, and creates its skills and databases too. A name already taken answers `409`, a plan limit `402`.
3. Else a new agent, `POST /api/v1/agents` with a `name` in lowercase letters, numbers and single hyphens, a `description` and `instructions`.

An installed template's skills carry bracketed placeholders, such as `[billing page]`, listed under the template's `placeholders`. Find each in the `instructions` from `GET /api/v1/skills?limit=100`, replace it with what the repository says, and `PATCH /api/v1/skills/{id}` with the whole `instructions`. Then `PATCH /api/v1/agents/{id}` with `{}`, so its threads pick up the edited skills. Leave a placeholder the repository doesn't answer, and keep it for the hand-off.

## 4. The project

`POST /api/v1/projects` with the app's `name`, the agent as `defaultAgentId`, and `allowedOrigins` naming every origin the app runs on in development, read from its dev script and framework (`http://localhost:3000` for Next.js, `http://localhost:5173` for Vite). `allowedOrigins` replaces the default list.

Write the answer's `publishableKey` to the variable `useAssistedRuntime({ key })` reads, in the app's `.env.local`. Unlike an organization key, it ships in the app's bundle.

## 5. Sync

Run [sync.md](sync.md) for this app.

## 6. Journeys

Run [journey.md](journey.md) without a goal: propose up to three, and create the ones the user picks.

## Done

Done when every call answered 2xx, the key is in the env file, and the page catalog matches the scan. The hand-off lists:

- the agent and project, reused or created, and the project's page in the dashboard;
- the placeholders left, for whoever finishes in the dashboard;
- the journey drafts, whose trigger a member sets and publishes on the project's **Journeys** page;
- on the curl path, that the organization key stays in `.env.local` and can be revoked on **API keys**.
