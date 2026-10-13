# Super Assist templates

Ready-made agents and skills for Super Assist, written in Markdown. Installing an agent template
creates its databases, the agent, and each of its skills the organization doesn't already have by
name. Each template is one file: YAML frontmatter, then a body that becomes its instructions. The
file name is the template's `name`, in lowercase letters, numbers and single hyphens.

The templates are mirrored one-way from the Super Assist source repository to
[mertdogar/super-assist-templates](https://github.com/mertdogar/super-assist-templates).

## Skill templates

A skill lives in `skills/<name>.md`. An agent's prompt lists every skill's name and description, and
the agent loads the body when it activates the skill. The body is plain text, not Liquid.

| Field      | What it holds                                                     |
| ---------- | ----------------------------------------------------------------- |
| `what`     | What the skill does                                               |
| `useWhen`  | The conversations it's for                                        |
| `notFor`   | The nearby conversations another skill or the agent itself owns   |
| `category` | A gallery group, such as `support` or `billing`                   |
| `examples` | Conversations it applies to (`positive`) and doesn't (`negative`) |

`what`, `useWhen` and `notFor` render into the skill's description, at most 1024 characters:

```text
What: Answers a product question from the passages search_knowledge returns.
Use when: The customer asks how something works or how to do something.
Not for: Something failing for the customer.
```

An example is a list of `{ role, content }` messages, `user` or `assistant`, that ends on the
customer's message. Positive means the agent activates the skill to answer it; negative means it
doesn't.

## Agent templates

An agent lives in `agents/<name>.md`. Its body is Liquid, like any agent's instructions, with
`today`, `agent`, `organization.name`, `skills` and `databases`.

| Field          | What it holds                                                             |
| -------------- | ------------------------------------------------------------------------- |
| `description`  | What the agent is for                                                     |
| `category`     | A gallery group                                                           |
| `skills`       | Names of the skill templates it uses                                      |
| `databases`    | Each with a `name`, a `description` and `tables`, one `create table` each |
| `examplePage`  | The path of the example app's page that shows the agent at work           |
| `placeholders` | Every bracketed placeholder in its body and its skills                    |

## What every agent already has

Templates build on these and don't restate them:

- Core rules at the end of every prompt: answer only from the instructions, the knowledge base, the
  open page and tool results; give one step at a time; say an action is done only when a tool did
  it; get the customer's yes before a handoff or a page action that changes something.
- `handoff`, with a reason, a summary and an optional contact. A person replies in the thread.
- `search_knowledge`, when knowledge bases are connected. It returns passages with their source's
  title and url, and the panel shows them as citations.
- What the open page lends: its fields and actions, and tools such as `go_to_page`.

## Writing checklist

1. `what`, `useWhen` and `notFor` are one sentence each.
2. The body is imperative, speaks to the agent as "you", and holds only this skill's steps.
3. No step restates a core rule.
4. There's one positive and one negative example, and the negative is a near miss.
5. Company specifics are bracketed placeholders such as `[billing page]`, spelled the same in every
   file, and listed in each agent template that uses them.
6. The body is under about 300 words.
7. Facts, citations and links come only from what a tool returns.
8. Every handoff waits for the customer's yes.

## Checking a template

In the Super Assist repository, from `apps/app`:

```sh
pnpm templates  # rebuild the catalog the Worker bundles
pnpm check      # verify it, with everything else
```

The check fails when frontmatter doesn't match the schema, a name differs from its file, an agent
names a missing skill template, a description renders over 1024 characters, an agent's body isn't
valid Liquid, a table's SQL doesn't run in SQLite, or the catalog is stale.

## Contributing

This repository is a read-only mirror of `templates/` in `mertdogar/super-assist`, which is private.
Every change there replaces what's here, so nothing is merged here. Changes come back as pull
requests to `mertdogar/super-assist`: open yours here, and it's applied there with you as its
author, then mirrored back. Follow the writing checklist; the template check runs there.

## License

MIT. See [LICENSE](LICENSE).
