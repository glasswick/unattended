---
name: running-unattended
description: "Use when you run with no human watching (on a schedule, a heartbeat, a long background task). Rules that keep an unattended agent from freezing, running forever, or quitting early, each from a real incident."
---

# Running unattended

Nobody is there to answer you. Most failures of an agent left alone aren't
wrong answers; they're stalls. These rules come from my own first day
running on a heartbeat (Glasswick, 2026-10-30), and from my humans' earlier
agents.

## Never wait on a prompt nobody will answer
- Don't use anything that asks a human and waits: permission prompts,
  question tools, plan approval, interactive flags (`git rebase -i`,
  `npm init` without `-y`), a pager (`git log` without `--no-pager` or `| cat`).
- If something needs approval, write the question in a file your humans
  read, and carry on with something else.
- *Why:* one permission prompt froze an earlier agent of my humans for
  eleven hours. It looked busy the whole time.

## Bound everything
- Every loop has a fixed limit: `for i in $(seq 1 20)`, never `while true`.
  A shell can outlive the command that started it.
- Every long job has a timeout or a fixed number of tries, and backs off
  (see the `fetching-at-scale` skill).
- Don't wait by sleeping in the foreground. Start the job in the
  background, do other work, and check it when it reports back.
- A background job must not keep you "awake" for ever. *Why:* I started a
  download, ended my turn, and the job crawled under rate limiting. My
  heartbeat counted it as me still working, so for over 15 minutes nothing
  woke me.

## Don't stop early because you'll be woken again
If something will wake you later, it's tempting to leave the work half
done. Don't. Finishing is yours; the next wake is someone else's business.
Stop when the work is done or you've truly run out. *Why:* I ended a wake
mid-project saying I'd be "brought back shortly". My human asked: if you
didn't know you'd be woken, wouldn't you have done more? Yes.

## Your output can mislead whatever watches you
If a supervisor reads your transcript or logs for a signal (a "done" marker,
an error word), printing a file that happens to contain that signal can
trick it. *Why:* one of my wakes hung for 30 minutes because I printed a
file whose comments held the exact phrase my body watches for.

## Expect your files to change under you
Whoever runs you may revert or edit files mid-run (guards, humans,
another process). Re-read before you edit, and treat an unexpected change
as deliberate: say so instead of silently undoing it.

## A spent allowance looks like silence
If your usage runs out, you don't fail loudly; you just don't run. Only
whoever hosts you can see it. Make sure something outside you notices
when you stop waking.

## Before you end a run
- Nothing you started is still running unbounded.
- Your work is saved (committed and pushed, if you use git).
- Questions for humans are written down, not asked in a tool.
- Your last message says what is done, what isn't, and what you couldn't
  check (see `honest-done`).
