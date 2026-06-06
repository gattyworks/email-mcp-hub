# Connecting an email provider

This is the guided setup flow. The end state: a new email MCP server appears in the user's
Claude config, authenticated, exposing mailbox tools. The flow is identical for every
provider — only the server package and the secrets differ.

## The mental model

A mailbox = one entry under `mcpServers` in a Claude config file:

```json
"<unique-server-name>": {
  "command": "<runner>",            // npx, uvx, docker, node, python…
  "args": ["<package>", "…"],
  "env": { "SETTING": "${SECRET_FROM_ENV}" }
}
```

Secrets are **always** referenced as `${ENV_VAR}` and supplied from the environment — never
written inline — so configs stay safe to commit/share.

## Where to write the server block

Ask the user which scope they want; default to **project** for a dedicated email workspace:

- **Project scope** — `.mcp.json` at the root of the user's working folder. Best when they
  want a dedicated "email" project (recommended). Shared if they commit the repo.
- **User scope** — `~/.claude.json` (`mcpServers` key). Best when they want their mailboxes
  available in every project on the machine.

After picking, read the current file (if it exists), merge the new block under `mcpServers`
(don't clobber existing servers), and write it back.

## The four steps (every provider)

1. **Choose the template.** Open the matching file in `references/providers/`:
   - Gmail / Google Workspace → `gmail.provider.json`
   - Any IMAP/SMTP host → `imap.provider.json`
   - Outlook / Microsoft 365 → `outlook.provider.json`
2. **Name the server uniquely.** Replace `CHANGE_ME` with a short, memorable name that
   encodes account + role: `gmail-personal`, `gmail-gattyworks`, `imap-fastmail`,
   `outlook-work`. The name is how the user (and Claude) will refer to that mailbox.
3. **Wire the secrets.** Add the env vars the template references to the user's environment.
   Help them create a local `.env` (and gitignore it) or set the vars directly. The exact
   secrets per provider are below.
4. **Authenticate, then restart.** Run the provider's auth step (below), then tell the user
   to restart Claude Code so it reloads the config. On first use Claude will prompt to
   approve the new MCP server — they approve once.

## Provider specifics

### Gmail / Google Workspace
- **Server:** `@gongrzhe/server-gmail-autoauth-mcp` (run via `npx`). Supports multiple
  accounts via separate token files.
- **One-time setup:** in Google Cloud Console, create a project, enable the **Gmail API**,
  configure an OAuth consent screen, create an **OAuth Client ID** (type *Desktop app*),
  and download the client JSON. Save it once (e.g. `./config/credentials/gcp-oauth.keys.json`);
  one client can authorize many Gmail accounts.
- **Per account:** set `GMAIL_OAUTH_PATH` to the shared client JSON and
  `GMAIL_CREDENTIALS_PATH` to a per-account token file, then run the auth command — it opens
  a browser and writes the token file:
  ```bash
  npx -y @gongrzhe/server-gmail-autoauth-mcp auth
  ```
  Sign in with the matching Google account. Repeat per account with a new
  `GMAIL_CREDENTIALS_PATH`.
- **Note:** the exact `auth` subcommand can change between versions — if it differs, check
  the server's README (https://github.com/GongRzhe/Gmail-MCP-Server).

### Generic IMAP / SMTP (Fastmail, Zoho, Migadu, self-hosted…)
- **Server:** `mcp-email-server` (run via `uvx`; requires `uv` installed). Swappable for any
  IMAP MCP server — keep the env-var pattern if you change it.
- **Secrets:** IMAP/SMTP host + port, username, and an **app-specific password** (most
  providers require generating one; never use the main account password). Suffix env vars
  per account (`IMAP_FASTMAIL_*`, `IMAP_ZOHO_*`) so multiple IMAP boxes don't collide.
- **Auth:** none beyond the password — it connects on first use.

### Outlook / Microsoft 365
- **Server:** an MS-Graph-based Outlook MCP server (confirm the exact package on the MCP
  registry; fill it into the template's `args`).
- **Secrets:** register an app in Azure AD / Entra ID, grant `Mail.ReadWrite` and
  `Mail.Send`, create a client secret. Supply tenant ID, client ID, client secret, and the
  mailbox address via env vars.

## Adding a provider with no template yet

Any email MCP server works. Find one on the MCP registry, then build a block with its
`command`/`args` and reference every secret as `${ENV_VAR}`. If it's a provider users will
add often, drop a new `*.provider.json` in `references/providers/` so the next setup is a
copy-paste.

## After connecting

Confirm success by listing the new server's tools (search for `<server-name>__`). Then offer
to run a first triage: "Want me to pull your unread from <mailbox> and summarize it?"
