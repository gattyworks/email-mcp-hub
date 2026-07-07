# gattyworks marketplace

> **Archived.** This repository is no longer maintained and has been archived.

A [Claude Code](https://claude.com/claude-code) plugin marketplace by **gattyworks**.

## Plugins

### 📧 email-hub
Connect multiple email mailboxes (Gmail, Outlook, any IMAP/SMTP) to Claude via MCP and
process them — triage & summarize, draft & reply, and organize across all your inboxes.
Provider-agnostic and extensible. → [`plugins/email-hub`](./plugins/email-hub)

## Install

In Claude Code:

```
/plugin marketplace add gattyworks/email-mcp-hub
/plugin install email-hub@gattyworks
```

Restart Claude Code, then run `/email-connect` to hook up your first mailbox — or just say
*"connect my gmail and summarize what needs a reply."*

## Repo layout

```
.
├── .claude-plugin/
│   └── marketplace.json     # marketplace manifest (lists the plugins below)
└── plugins/
    └── email-hub/           # the email-hub plugin (skill + commands + templates)
```

To add more plugins later, drop them under `plugins/` and add an entry to
`.claude-plugin/marketplace.json`.

## License

MIT
