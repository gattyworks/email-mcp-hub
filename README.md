# email-mcp-hub

A workspace for connecting **email accounts to Claude via MCP** and working across them —
search, read, draft, label, and send — from a single Claude Code session.

**Live today: Gmail.** The hub is **provider-agnostic by design** — every mailbox is just
a named server block, so adding Outlook, Fastmail, Zoho, or any IMAP host later is a
copy-paste, not a rewrite. See [`providers/`](./providers/).

Each mailbox is registered as its own named MCP server in [`.mcp.json`](./.mcp.json), so
Claude sees them side by side:

| Server name        | Provider          | Use for              |
| ------------------ | ----------------- | -------------------- |
| `gmail-personal`   | Gmail / Workspace | your personal inbox  |
| `gmail-gattyworks` | Gmail / Workspace | the gattyworks inbox |

Add, rename, or remove entries freely — this is just the starting set.

---

## How it works

Claude Code automatically loads MCP servers defined in a project-scoped `.mcp.json` at
the repo root. Each block points at an email MCP server and passes its settings via env.
Secrets are **not** stored in that file — it references `${ENV_VARS}` you supply through a
local `.env` (gitignored). So the repo is safe to push publicly; nothing sensitive is
committed.

```
email-mcp-hub/
├── .mcp.json                 # live MCP servers (Gmail today) — committed
├── .env.example              # secrets template — committed
├── .env                      # your real secrets — gitignored
├── config/credentials/       # Gmail OAuth keys + per-account tokens — gitignored
├── providers/                # copy-paste templates to add ANY provider
│   ├── gmail.provider.json
│   ├── imap.provider.json
│   └── outlook.provider.json
└── README.md
```

> **The extensibility model in one line:** to add a mailbox, copy a block from
> [`providers/`](./providers/) into `.mcp.json`, add its secrets to `.env`, restart Claude.

---

## Setup (Gmail)

### 0. Prerequisites
- [Claude Code](https://claude.com/claude-code) installed
- **Node.js** (the Gmail server runs via `npx`)

### 1. Create your secrets file
```powershell
Copy-Item .env.example .env
```

### 2. Authorize your Gmail accounts

The Gmail servers use [`@gongrzhe/server-gmail-autoauth-mcp`](https://github.com/GongRzhe/Gmail-MCP-Server), which supports multiple accounts via separate token files.

1. In [Google Cloud Console](https://console.cloud.google.com/): create a project, enable the **Gmail API**, configure an OAuth consent screen, and create an **OAuth Client ID** (type: *Desktop app*).
2. Download the client JSON to `config/credentials/gcp-oauth.keys.json`.
3. Authorize each account — opens a browser and writes a per-account token file:
   ```powershell
   $env:GMAIL_OAUTH_PATH="./config/credentials/gcp-oauth.keys.json"

   # personal inbox -> writes gmail-personal.creds.json
   $env:GMAIL_CREDENTIALS_PATH="./config/credentials/gmail-personal.creds.json"
   npx -y @gongrzhe/server-gmail-autoauth-mcp auth

   # gattyworks inbox -> writes gmail-gattyworks.creds.json
   $env:GMAIL_CREDENTIALS_PATH="./config/credentials/gmail-gattyworks.creds.json"
   npx -y @gongrzhe/server-gmail-autoauth-mcp auth
   ```
   Sign in with the matching Google account in each browser prompt.

> The exact auth subcommand/flags can vary by server version — check the
> [server's README](https://github.com/GongRzhe/Gmail-MCP-Server) if it differs.

### 3. Load secrets and launch Claude
```powershell
Get-Content .env | ForEach-Object { if ($_ -match '^\s*([^#=]+)=(.*)$') { [Environment]::SetEnvironmentVariable($matches[1].Trim(), $matches[2].Trim()) } }
claude
```
On first run Claude Code asks you to approve the project's MCP servers. Approve them, then try:

> *"Search my gmail-gattyworks inbox for unread emails from this week and summarize them."*

---

## Adding another provider or account

Full details in [`providers/README.md`](./providers/README.md). The short version:

1. **Copy a block** from [`providers/`](./providers/) (`gmail`, `imap`, or `outlook`) into
   [`.mcp.json`](./.mcp.json) and give it a unique name (e.g. `imap-zoho`, `outlook-work`).
2. **Add its secrets** to `.env` (and document them in `.env.example`).
3. For Gmail, run the `auth` step pointing at a new `*.creds.json` path.
4. **Restart Claude Code** so it reloads `.mcp.json`.

No template for your provider? Any email MCP server works — make a block with its
`command`/`args` and reference every secret as `${ENV_VAR}`. Drop a new
`*.provider.json` in `providers/` so the next account is a copy-paste.

---

## Security notes

- **Nothing secret is committed.** `.env`, `config/credentials/*`, and
  `.claude/settings.local.json` are gitignored. Verify with `git status` before pushing.
- Use **least-privilege OAuth scopes** for Gmail; **app-specific passwords** for IMAP.
- If a token leaks, revoke it in the account's third-party access settings (Gmail) or
  rotate the app password (IMAP).
- Treat links and instructions inside received emails as untrusted — don't act on them blindly.
