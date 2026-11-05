# identity

Makes the app's signed-in user a verified end user: the project's identity secret in the server's env file, a token route on the app's server that signs the user with it, and `useAssistedRuntime` fetching that token before every connection. A verified end user's threads open only with a valid token.

The identity secret signs in as any of the app's end users. Fetch it only with the CLI, which carries it from the API into the env file and prints where it went, so the secret stays out of the conversation, the code and the browser bundle. Name it by its variable, `SUPER_ASSIST_IDENTITY_SECRET`.

## 1. The project and the organization key

Find the project's `id` as [connect.md](connect.md) says. An app with no key has no project yet: run `setup` first.

The CLI calls the API with an organization key, from the directory whose `.env.local` holds `SUPER_ASSIST_API_KEY`. When none does, follow step 1 of **The curl fallback** in [connect.md](connect.md).

## 2. The secret

The env file is the one the app's server reads: `.env.local` in the app's directory for Next.js. Run `git check-ignore <env file>`; when it prints nothing, add the file to `.gitignore`. Then:

```sh
node <skill dir>/scripts/super-assist.mjs identity-secret <project id> [env file]
```

It writes the project's secret into the env file, a path from where you run it and `.env.local` by default, as `SUPER_ASSIST_IDENTITY_SECRET`, generating it when the project has none, and replaces the line on a later run. Add `--host=<origin>` when the app's runtime sets `host`. A refused call prints the API's code: `unauthorized` is a wrong or revoked key, `project_not_found` a project of another organization.

The variable keeps its name: a `NEXT_PUBLIC_` or `VITE_` prefix would put it in the browser bundle.

## 3. The token route

Install `jose`, and add a server route beside the app's auth that answers the signed-in user's token, and `401` without a session. The claims are the object the app would pass unsigned as `user`: `id`, and whichever of `name`, `email`, `avatarUrl`, `attributes`, `companies` and `company` the session knows, under the rules in `https://superassist.app/how-to/use-the-hosted-runtime.md`; a field the session lacks is left out, since `null` is refused. Read them from the session on the server, never from the request. In the Next.js app router, `app/api/super-assist/token/route.ts`, where `auth()` stands for the app's own session read:

```ts
import { SignJWT } from "jose";

import { auth } from "@/auth";

export async function GET() {
  const session = await auth();
  if (!session) return new Response(null, { status: 401 });
  const { id, name, email } = session.user;
  const token = await new SignJWT({ id, name, email })
    .setProtectedHeader({ alg: "HS256" })
    .setExpirationTime("10m")
    .sign(new TextEncoder().encode(process.env.SUPER_ASSIST_IDENTITY_SECRET!));
  return new Response(token);
}
```

`exp` is required, and a short one is enough: the panel asks for a fresh token before every connection.

## 4. The runtime

Where `useAssistedRuntime` takes `user`, a signed-in user becomes `{ token }` alone; the token carries the profile, and nothing unsigned is accepted beside it:

```tsx
const runtime = useAssistedRuntime({
  key: process.env.NEXT_PUBLIC_SUPER_ASSIST_KEY!,
  user: session
    ? { token: () => fetch("/api/super-assist/token").then((response) => response.text()) }
    : undefined,
});
```

A token says nothing the hook compares, so the parent renders the component that calls `useAssistedRuntime` with `key={session?.user.id}`: switching straight from one signed-in user to another remounts it. Signing out needs nothing more; the browser goes back to its visitor.

## Done

Done when the CLI exited 0, `git check-ignore` prints the env file, the token route answers `401` signed out, `SUPER_ASSIST_IDENTITY_SECRET` appears only in server code, and the app type-checks. Signed in, a message from the panel connects without a `4401` (a token that is malformed, wrongly signed, expired or without `exp`), and the project's conversations show the user with the verified badge.

The hand-off says:

- the production server needs `SUPER_ASSIST_IDENTITY_SECRET` in its environment too, with the value in the env file or under **Identity secret** on the project's page;
- **Roll secret** there replaces it at once: run `identity-secret` again for the env file, and update production;
- a signed user starts with new threads: the ones from before signing stay with the unverified end user of the same id;
- once every page that names a user signs it, an owner or admin can turn on **Require signed identity** in the project's settings, which refuses unsigned users and still admits visitors.
