# email-mcp-hub

A workspace for connecting **multiple email accounts to Claude via MCP** and working
across them — search, read, draft, label, and send — from a single Claude Code session.

Each mailbox is registered as its own named MCP server in [`.mcp.json`](./.mcp.json), so
Claude sees them side by side:

| Server name        | Provider          | Use for                          |
| ------------------ | ----------------- | -------------------------------- |
| `gmail-personal`   | Gmail / Workspace | your personal inbox              |
| `gmail-gattyworks` | Gmail / Workspace | the gattyworks inbox             |
| `imap-fastmail`    | Generic IMAP/SMTP | any IMAP provider (Fastmail etc) |

Add, rename, or remove entries freely — the table above is just the starting set.

---

## How it works

Claude Code automatically loads MCP servers defined in a project-scoped `.mcp.json`
at the repo root. Secrets are **not** stored in that file — it references `${ENV_VARS}`
that you supply via a local `.env` (gitignored). So the repo is safe to push publicly;
nothing sensitive is committed.

```
email-mcp-hub/
├── .mcp.json                 # MCP server definitions (one per mailbox) — committed
├── .env.example              # template for secrets — committed
├── .env                      # your real secrets — gitignored
├── config/credentials/       # Gmail OAuth keys + per-account tokens — gitignored
└── README.md
```

---

## Setup

### 0. Prerequisites
- [Claude Code](https://claude.com/claude-code) installed
- **Node.js** (for the Gmail server, run via `npx`)
- **[uv](https://github.com/astral-sh/uv)** (for the IMAP server, run via `uvx`) — `pip install uv` or `winget install astral-sh.uv`

### 1. Create your secrets file
```powershell
Copy-Item .env.example .env
```
Then edit `.env` with your real values.

### 2. Gmail accounts

The Gmail servers use [`@gongrzhe/server-gmail-autoauth-mcp`](https://github.com/GongRzhe/Gmail-MCP-Server), which supports multiple accounts via separate token files.

1. In [Google Cloud Console](https://console.cloud.google.com/): create a project, enable the **Gmail API**, configure an OAuth consent screen, and create an **OAuth Client ID** (type: *Desktop app*).
2. Download the client JSON and save it to `config/credentials/gcp-oauth.keys.json`.
3. Authorize each account — this opens a browser and writes a per-account token file:
   ```powershell
   # personal inbox -> writes gmail-personal.creds.json
   $env:GMAIL_OAUTH_PATH="./config/credentials/gcp-oauth.keys.json"
   $env:GMAIL_CREDENTIALS_PATH="./config/credentials/gmail-personal.creds.json"
   npx -y @gongrzhe/server-gmail-autoauth-mcp auth

   # gattyworks inbox -> writes gmail-gattyworks.creds.json
   $env:GMAIL_CREDENTIALS_PATH="./config/credentials/gmail-gattyworks.creds.json"
   npx -y @gongrzhe/server-gmail-autoauth-mcp auth
   ```
   Sign in with the matching Google account in each browser prompt.

> The exact auth subcommand/flags can vary by server version — check the
> [server's README](https://github.com/GongRzhe/Gmail-MCP-Server) if it differs.

### 3. IMAP/SMTP accounts

The `imap-fastmail` server uses [`mcp-email-server`](https://github.com/ai-zerolab/mcp-email-server) (any IMAP provider). Fill in host/user/password in `.env`. **Use an app-specific password**, not your login password. Copy the block in `.env` to add more IMAP mailboxes (Zoho, Migadu, custom, …) and add a matching entry in `.mcp.json`.

> Prefer a different IMAP MCP server? Swap the `command`/`args` of the `imap-*`
> entry in `.mcp.json` — the structure stays the same.

### 4. Load secrets and launch Claude
```powershell
# Load .env into the current shell, then start Claude from this folder
Get-Content .env | ForEach-Object { if ($_ -match '^\s*([^#=]+)=(.*)$') { [Environment]::SetEnvironmentVariable($matches[1].Trim(), $matches[2].Trim()) } }
claude
```
On first run Claude Code asks you to approve the project's MCP servers. Approve them, then try:

> *"Search my gmail-gattyworks inbox for unread emails from this week and summarize them."*

---

## Adding another account

1. Add a server block in [`.mcp.json`](./.mcp.json) with a unique name.
2. Add its secrets to `.env` (and document them in `.env.example`).
3. For Gmail, run the `auth` step pointing at a new `*.creds.json` path.
4. Restart Claude Code so it reloads `.mcp.json`.

---

## Security notes

- **Nothing secret is committed.** `.env`, `config/credentials/*`, and
  `.claude/settings.local.json` are gitignored. Verify with `git status` before pushing.
- Use **app-specific passwords** for IMAP, and **least-privilege OAuth scopes** for Gmail.
- If a token leaks, revoke it in the Google account's *Third-party access* settings (Gmail) or rotate the app password (IMAP).
- Treat links inside emails as untrusted — don't open them blindly.
