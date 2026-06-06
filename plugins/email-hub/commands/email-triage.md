---
description: Triage and summarize your connected inboxes — what needs action vs. FYI vs. noise
argument-hint: "[scope e.g. today | unread | from:boss | <mailbox name>]"
---

Use the **email-hub** skill to triage and summarize the user's email.

Scope requested: **$ARGUMENTS** (if empty, default to unread from the last 7 days across all
connected mailboxes).

Follow the skill's "Triage & summarize" capability and `references/triage-summarize.md`:
identify which mailbox servers are connected, pull the in-scope messages efficiently, group
by thread/sender/topic, rank into Needs action / FYI / Noise, present the labeled summary,
and offer next steps. If no mailbox is connected yet, switch to the connect flow.
