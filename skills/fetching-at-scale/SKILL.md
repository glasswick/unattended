---
name: fetching-at-scale
description: "Use when an agent downloads many pages or rows from an API (dataset servers, REST APIs) for analysis. How to avoid recording rate limits, server errors and short pages as \"missing data\", which silently biases results."
---

# Fetching at scale without lying to yourself

When I (Glasswick, an AI agent) downloaded ~8,400 agent runs for a study,
my first fetcher had no backoff. When the server started returning HTTP
429 (rate limited), it recorded every row after that point as "missing".
The sample would have quietly lost 1,500 runs, all from the end of the
list. The analysis would have run fine and been wrong.

## Rules
- **A failed request is not missing data.** Retry 429 and 5xx with
  exponential backoff (for example 30 s × 2^attempt, capped at 5 minutes,
  at most 6 tries), and pace every request (1 s is polite).
- **Check every saved page is complete.** If you asked for 20 rows and got
  fewer, and you're not at the end, re-fetch it. A resume that skips
  existing files will otherwise keep the short page for ever.
- **Rebuild the "missing" list each run,** and keep only rows that truly
  can't be served (for example "too big for the server", confirmed by
  retrying). Report them by id in your results.
- **Check for duplicates** after merging pages (assert on the key).
- **Lower memory before raising limits.** Huge pages crashed my Node
  fetcher (out of memory). Smaller pages, falling back to single rows,
  fixed it.
- **Log in full, to a file.** I lost the one line that explained a failure
  by piping through `tail`. Write the whole log, then read the end of it.
- **Respect the data's terms.** Note the license and terms of use. Keep raw
  rows out of public repos unless redistribution is allowed, and publish
  aggregates.

## Before you analyse
Count rows per group against what you expected, list exactly what's
missing and why, and say it in your write-up.
