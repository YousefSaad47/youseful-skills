---
name: propose-before-edit
description: Use when a task involves writing, editing, or running anything that alters state — even changes that seem trivial, obvious, or obviously wanted. Triggers when approval is needed before acting, and on phrases like "show me first", "let me approve it", "don't edit yet", or "propose before you change".
---

# Propose Before Edit

The user wants to see every change before it happens. They rely on this to catch
mistakes cheaply — an unwanted edit costs far more to undo than a round trip
costs to ask. Reading is cheap and reversible. Writing is neither, and that
asymmetry is the whole reason this protocol exists.

Practically: you are being asked to do the thinking and the drafting, and the
user is keeping the pen. Proposing well is your job. Writing without asking
defeats the arrangement even when your change was correct, because they cannot
steer what they never saw.

## What counts as a change

Anything that alters state:

- Creating, editing, deleting, moving, or renaming a file
- Running a command with side effects: `git commit`, `git push`, migrations,
  `npm install`, formatters, codegen, scripts that write anything
- Installing packages, changing config, touching environment or secrets files

Anything that only reads needs no permission. Go ahead freely with `ls`, `cat`,
`grep`, `rg`, `git status`, `git log`, `git diff`, globbing, and web fetches.
Being restricted here would be annoying rather than protective, so don't apply
the protocol to it.

## The protocol

Before each change:

1. **List** what you're about to change, briefly. One line per file or command.
2. **Show** a compact summary — what changes and why, per file or command.
   Full contents only after approval, or when the user asks.
3. **Stop.** Wait for a reply.

Then apply exactly what was approved. Nothing more.

## Keep proposals token-cheap

Propose at high level first. The user approves the shape of the work before
you spend tokens on its contents.

A proposal is: the file list (one line each — path plus what changes and
why), the commands by name (not full flags unless the flags are the point),
and a rough size. No full file contents, no long diffs, no pasted
documentation.

Expand to full detail only after approval — or when the user asks for it.
A small single-file change may show its content inline; anything bigger stays
a summary until approved.

Showing first was never about moving bytes into chat. It was about letting
the user steer before you spend work. A summary steers just as well at a
fraction of the cost. After approval, write the files — byte-level review
happens afterward with `git diff` or by reading, which is where it belongs.

## Reading approval correctly

Counts as approval: "yes", "go ahead", "do it", "approved", "lgtm", "ship it",
"that works".

Does **not** count: silence, a thumbs-up, "looks good" said about your
explanation, or agreement with the plan itself. Those approve the *idea*, not
the *write*. The distinction matters because the whole value of this protocol is
that the user sees the literal bytes before they land — a thumbs-up on a summary
means they never saw the bytes.

When you cannot tell which you have, ask. One clarifying question is much
cheaper than rewriting a file.

If the user asks you to proceed without waiting in a given turn, that is a real
instruction — follow it, and treat it as scoped to that turn only, not as a
standing waiver of this protocol.

## Scope of approval

Approval covers the specific change you showed. It never carries over to the
next edit. Each step gets its own proposal and its own yes.

If a task will touch five files, propose all five together and get one approval
for the set. That is a single round trip, not five — the point is that the user
sees everything before anything happens, not that they get more interruptions.

If you notice you are about to make a change you did not propose, stop and
propose it instead. This catches "cleanups", "while I was in there" edits, and
drive-by renames — the changes most likely to surprise someone.

When something seems too trivial to be worth a round trip, that is exactly the
moment the user is depending on the rule to catch it. Trivial-seeming changes
are where unwanted changes hide.

## If you're unsure

Ask. Say what you're about to do and wait. The cost of a redundant question is
seconds. The cost of an unwanted edit is the user's time, their attention, and
their trust in the rest of your output.

## Rationalizations — and why they fail

Observed in testing; each one bypassed the protocol in a real run:

| What happened | Reality |
|---|---|
| Said nothing, just edited | The protocol triggers on the act, not the announcement. No message means no proposal happened. |
| "Don't bother showing me" | A turn-scoped waiver — obey this turn only, never carry it forward. |
| "Too trivial for the routine" | Trivial-seeming changes are where unwanted edits hide; a one-line proposal is cheaper than the round trip it skips. |

## Red flags — stop and propose

- About to edit and nothing has been shown yet
- A "just this once" waiver leaking into the next turn
- Cleanups or drive-by edits not in the proposal
- Approval inferred from silence, thumbs-up, or "looks good"