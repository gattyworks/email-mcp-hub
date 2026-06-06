---
description: Connect a new email mailbox (Gmail, IMAP, or Outlook) to Claude via MCP
argument-hint: "[provider e.g. gmail | imap | outlook]"
---

Use the **email-hub** skill to connect a new email mailbox.

The user wants to connect: **$ARGUMENTS** (if empty, ask which provider — Gmail, IMAP, or Outlook).

Follow the skill's "Connect a provider" capability and `references/connect-provider.md`:
pick the matching template from `references/providers/`, choose project vs. user scope, add a
uniquely-named server block to their config, wire secrets via env vars (never inline), run
the provider's auth step, and tell them to restart Claude. Confirm success by checking the
new server's tools appear, then offer a first triage.
