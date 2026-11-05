# doctor

Reports how far the app is from a working Super Assist setup, and names the command that closes each gap. It changes nothing: no code, no env file, no call that writes.

## The checks

Run them in order; checks 1 to 4 read only the repository and the tool list, 5 to 7 read Super Assist in one `execute`. A failed check skips the ones that need it.

1. **The library.** The app's `package.json` depends on `@super-assist/react`; the version installed is in `node_modules/@super-assist/react/package.json`, and the newest in `npm view @super-assist/react version` when the network allows. Missing → `setup`.
2. **The key.** The variable `useAssistedRuntime({ key })` reads holds a `pk_…` value in the app's env files. Missing → `setup`.
3. **The code.** The scan runs; `destinations.missing`, `destinations.wrong` and `dynamic` are empty, or list what `sync` fills and corrects. A route that renders no `AssistedPage` → `page <route>`.
4. **The connection.** The MCP server's `search` and `execute` are in the tool list ([connect.md](connect.md)). Missing → connect as it says.
5. **The project.** `GET /api/v1/projects` has a project with the key as its `publishableKey`. Missing → the reasons in [connect.md](connect.md), or `setup` for a new one. Its `requireSignedIdentity` on while the code passes an unsigned `user` refuses every signed-in user → `identity`.
6. **Catalog drift.** `GET /api/v1/projects/{id}/pages` against the scan's `body`: pages the code has and the catalog lacks, pages the catalog has and the code doesn't, and per page the names, instructions and elements that differ. Any → `sync`.
7. **Journeys naming what's gone.** `GET /api/v1/projects/{id}/journeys`: steps, draft or published, whose `path` the scan's `body` has no page for, or whose `element` that page doesn't lend. Any → `sync`, which asks about each.

## Report

One line per check: what it found, and for a failure the command that closes it. When every check passes, say so in one line. Run a command the report names only when the user asks for it.
