# Reaching Super Assist

Every command that calls Super Assist goes through its MCP server first, finds the app's project by its publishable key, and falls back to an organization key with curl only when the user picks it.

## The MCP server

The server at `https://superassist.app/mcp` has two tools. In Claude Code, with the plugin, they are `mcp__plugin_super-assist_api__search` and `mcp__plugin_super-assist_api__execute`; in another client, they are the `search` and `execute` of the server the user added at that URL.

- `search` runs JavaScript over the API's OpenAPI spec. Use it to look up a route's parameters, body and answer before you write the call.
- `execute` runs JavaScript in a sandbox whose `request({ method, path, query, body })` calls a `/api/v1` route as the signed-in member, in the organization they picked at sign-in. It returns the answer's JSON; a refused call throws an error naming the method, path and status with the API's `{ code, message, details }`. The tool's description shows how the code calls `request()`.
- The user approves each `execute`, so put a step's calls in one: read what you need, write, and return what the next step needs.

**Not connected.** When neither tool is in your tool list, stop and ask the user to connect; carry on only once the tools are there, or on the curl path when the user picks it.

- In Claude Code, the server is `plugin:super-assist:api`. While it waits for sign-in, Claude Code lists `mcp__plugin_super-assist_api__authenticate` in its place: offer to call it, and give the user the URL it returns. Otherwise the user runs `/mcp`, selects `plugin:super-assist:api` and signs in in the browser.
- In Codex, Cursor, VS Code, Claude and ChatGPT, the user adds an MCP server at `https://superassist.app/mcp` in the client's MCP settings and signs in.
- Signing in, the member picks one organization when they belong to several, and allows the client once. The connection acts as them in that organization until they disconnect it under **Account** › **Coding agents** in the dashboard. Switching organizations means signing in again: in Claude Code, `/mcp` › `plugin:super-assist:api` › **Clear authentication**.

## The project

The app names its project by the publishable key it passes to `useAssistedRuntime({ key })`.

1. Read the variable that `key` reads, such as `NEXT_PUBLIC_SUPER_ASSIST_KEY` or `VITE_SUPER_ASSIST_KEY`, and find its `pk_…` value in the app's env files (`.env.local`, `.env.development.local`, `.env.development`, `.env`).
2. `GET /api/v1/projects`, paging until `nextCursor` is null, and keep the project whose `publishableKey` is that value.

No key means the app has no project yet: `setup` creates one. A key with no project means the connection is to another organization, the key was rolled, or the app's `host` points at another Super Assist origin; say which you can tell, and stop.

## The curl fallback

When the user prefers an organization key over the MCP server, or the client can't connect, follow `https://superassist.app/how-to/set-up-through-the-api.md` from the app's repository:

1. Run `git check-ignore .env.local`; when it prints nothing, add `.env.local` to `.gitignore`. Ask the user, an owner or admin of the organization, to create a key on the **API keys** page and paste it into `.env.local` as `SUPER_ASSIST_API_KEY`.
2. The key lives only in `.env.local`. Read it inside each command with `key=$(sed -n 's/^SUPER_ASSIST_API_KEY=//p' .env.local)` and send it only as `Authorization: Bearer $key`, without `-v`. Never print it, and never write it into code.
3. The routes, bodies and answers are the ones `request()` takes. An organization key can't publish a journey or an article (`403`): a member publishes it on the project's **Journeys** or **Help center** page.

The hand-off says the key stays in `.env.local` for later calls and can be revoked on the **API keys** page.
