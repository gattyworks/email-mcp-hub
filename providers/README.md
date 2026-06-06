# Providers

This folder is the **extension point** for the hub. Today only **Gmail** is wired up
live in [`../.mcp.json`](../.mcp.json), but the design is provider-agnostic: every
mailbox — Gmail, Outlook, Fastmail, Zoho, a self-hosted IMAP box — is just a named
server block with the same shape.

```json
"<unique-server-name>": {
  "command": "<runner>",          // npx, uvx, docker, node, python ...
  "args": ["<package-or-script>", "..."],
  "env": { "...": "${SECRETS_FROM_ENV}" }
}
```

Claude doesn't care which provider is behind a server — it just sees a set of mailbox
tools. That's what makes the hub extensible: **add a block, add its secrets, restart.**

## The templates here

Each `*.provider.json` is a ready-to-copy server block plus the env vars it needs.
They are **reference snippets, not loaded config** — nothing in this folder is read by
Claude. Only [`../.mcp.json`](../.mcp.json) is.

| Template                                       | Provider              | Status        |
| ---------------------------------------------- | --------------------- | ------------- |
| [`gmail.provider.json`](./gmail.provider.json) | Gmail / Workspace     | **live**      |
| [`imap.provider.json`](./imap.provider.json)   | Any IMAP/SMTP host    | ready to add  |
| [`outlook.provider.json`](./outlook.provider.json) | Outlook / Microsoft 365 | ready to add |

## How to add a provider (3 steps)

1. **Copy the block.** Open the matching `*.provider.json`, copy the entry under
   `"mcpServers"`, and paste it into [`../.mcp.json`](../.mcp.json). Give it a unique
   name (e.g. `imap-zoho`, `outlook-work`).
2. **Add its secrets.** Copy the template's env vars into your `.env` (and document
   them in [`../.env.example`](../.env.example)) with real values.
3. **Restart Claude Code** so it reloads `.mcp.json`. Approve the new server when prompted.

## Adding a provider that has no template yet

Any email MCP server works. Find one (e.g. on the MCP registry), then create a block:
- `command`/`args` = however that server is launched.
- `env` = its required settings, with **every secret** referenced as `${ENV_VAR}` so it
  stays out of git.

If you add a new provider regularly, drop a `*.provider.json` template here so the next
account is a copy-paste.
