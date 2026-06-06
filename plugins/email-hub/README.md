# email-hub

A Claude Code plugin to connect **multiple email mailboxes** to Claude via MCP and work
across them — **Gmail, Outlook/Microsoft 365, or any IMAP/SMTP provider** — all from one
session.

Once installed, Claude can:

- **Connect a provider** — guided setup that adds an email MCP server to *your* config and
  walks you through auth (OAuth for Gmail, app-password for IMAP, Azure app for Outlook).
- **Triage & summarize** — pull recent/unread across all your inboxes and sort into
  *needs action / FYI / noise*.
- **Draft & reply** — context-aware replies it shows you for approval; **it never sends
  without your explicit OK.**
- **Organize** — label, archive, and mark read in bulk, with reversible-by-default actions.

It's **provider-agnostic**: every mailbox is just a named MCP server, so adding a new vendor
or a second account is a copy-paste of a template (see
[`skills/email-hub/references/providers/`](./skills/email-hub/references/providers/)).

## Install

From Claude Code:

```
/plugin marketplace add gattyworks/email-mcp-hub
/plugin install email-hub@gattyworks
```

Then restart Claude Code.

## Use

Just talk to Claude, or use the slash commands:

| Command           | What it does                                            |
| ----------------- | ------------------------------------------------------- |
| `/email-connect`  | Connect a new mailbox (Gmail / IMAP / Outlook)          |
| `/email-triage`   | Summarize inboxes — what needs action vs. FYI vs. noise |
| `/email-organize` | Bulk label / archive / mark read                        |

Natural language works too — *"connect my gmail"*, *"what needs a reply today?"*,
*"clean up my inbox"* all trigger the skill.

> **First time?** Run `/email-connect gmail` (or `imap` / `outlook`). You'll need the
> provider prerequisites — a Google Cloud OAuth client for Gmail, an app-specific password
> for IMAP, or an Azure app registration for Outlook. The connect flow walks you through each.

## What's inside

```
email-hub/
├── .claude-plugin/plugin.json     # plugin manifest
├── commands/                      # /email-connect, /email-triage, /email-organize
└── skills/email-hub/
    ├── SKILL.md                   # the playbook Claude follows
    └── references/
        ├── connect-provider.md    # guided setup, per provider
        ├── triage-summarize.md
        ├── draft-reply.md
        ├── organize.md
        └── providers/             # copy-paste MCP server templates
            ├── gmail.provider.json
            ├── imap.provider.json
            └── outlook.provider.json
```

## Security

The plugin ships **no credentials and no live servers** — it only knows *how* to set them
up. Your secrets stay in your own environment (`.env` / OS env vars), referenced as
`${ENV_VARS}` from your MCP config and never committed. The skill is built to **confirm
before sending or deleting**, treat email content as untrusted (prompt-injection aware),
and never echo secrets.

## License

MIT
