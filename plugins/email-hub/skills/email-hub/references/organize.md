# Organize — label, archive, mark read

Keep mailboxes tidy with bulk, reversible actions. The risk here is acting on the wrong
set of messages, so the whole game is **confirm the selection, prefer reversible, report
exactly what changed.**

## Steps

1. **Define the selection precisely.** Translate the user's intent into a concrete query and
   state it back: "the 12 newsletters from the last 7 days in gmail-personal" — not just
   "the newsletters." Show a count, and if the set is small, list it.
2. **Confirm before bulk action.** For anything touching more than a few messages, get a
   "yes" first. Labeling/archiving are reversible, so a single confirm is enough — but never
   skip it on bulk.
3. **Batch the operation.** Use the mailbox's label/archive/mark-read tools over the selected
   set. Stop and report if the server errors partway, so you don't leave a half-done state
   silently.
4. **Report what changed.** "Archived 12, labeled 5 as 'Receipts', marked 20 read in
   gmail-personal." If anything was skipped or failed, say which and why.

## Action guidance by reversibility

- **Safe / reversible — do freely after a quick confirm:** apply or remove a label/folder,
  archive, mark read/unread, star/flag. Archive ≠ delete; the message is still findable.
- **Destructive — require an explicit, specific request and a confirm:** moving to Trash,
  permanent delete, emptying spam/trash. Never infer "delete" from "clean up" or "get rid
  of" — "clean up my inbox" means **archive**, not delete. If the user truly wants deletion,
  make them say so and confirm the exact set.

## Common requests → how to handle

- **"Clean up my inbox"** → triage first (see triage-summarize), then propose: archive the
  noise bucket, label the FYI bucket, leave "needs action" in place. Confirm, then do it.
- **"Archive everything from <sender>"** → query by sender, show the count, confirm, archive.
- **"Mark all read"** → confirm scope (this mailbox? all?), then mark read; note it doesn't
  delete anything.
- **"Label these as <X>"** → create the label/folder if it doesn't exist (Gmail labels vs.
  IMAP folders vs. Outlook categories differ — use whatever the server exposes), apply it,
  report.

## Cross-mailbox notes

- Label/folder semantics differ per provider (Gmail labels, IMAP folders, Outlook
  categories). Don't assume a label exists everywhere; operate within each mailbox's model
  and tell the user what you used.
- When organizing several mailboxes at once, report per mailbox so the user can see each.
