---
description: Organize mailboxes in bulk — label, archive, or mark read across inboxes
argument-hint: "[e.g. archive newsletters | label receipts | mark all read]"
---

Use the **email-hub** skill to organize the user's mail.

Request: **$ARGUMENTS** (if empty, ask what they'd like to tidy — archive noise, label a
group, mark read, etc.).

Follow the skill's "Organize" capability and `references/organize.md`: translate the request
into a precise selection, state the count back and confirm before any bulk action, prefer
reversible actions (archive/label/mark-read — never delete unless explicitly asked), batch
the operation, and report exactly what changed per mailbox.
