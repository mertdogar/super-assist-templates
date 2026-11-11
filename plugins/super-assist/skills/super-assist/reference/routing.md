# The menu

For `/super-assist` with no argument: the user is asking what to do next. Lead with what this app needs, then show every command.

1. Run checks 1 to 4 of [doctor.md](doctor.md): the library, the key, the code and the connection. They read only the repository and the tool list, so the menu asks for no approval.
2. Recommend the two or three commands that move this app furthest, each with the exact command to type and a one-line reason from what the checks found:
   - no library, or no key → `setup`;
   - not connected → connect first, as [connect.md](connect.md) says, then the command the user is after;
   - `destinations.missing`, `destinations.wrong` or `dynamic` not empty, or code changed since the last sync → `sync`, with the app's dir in a repository of several apps;
   - routes that render no `AssistedPage` → `page <route>`, naming the route;
   - `useAssistedRuntime` passes an unsigned `user` → `identity`, so Super Assist verifies who is signed in;
   - a key and a connection, with nothing else open → `journey` or `article`, or `doctor` for the catalog and journeys on Super Assist.
3. Show the Commands table from SKILL.md below the recommendations.

Run a command only once the user picks it.
