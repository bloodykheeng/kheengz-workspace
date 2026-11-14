---
description: Write a structured handoff note capturing the current state of work so another session can resume cleanly.
argument-hint: "[optional short title for the handoff]"
allowed-tools: Read, Glob, Grep, Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git branch:*), Write
---

# /handoff

Produce a **handoff note** for the work done this session, so a fresh session (or another
developer) can pick up exactly where things stand. Title hint: $ARGUMENTS

> ⚠️ **Make this session identifiable.** Sessions are auto-titled from your first reply, so
> every `/handoff` otherwise collapses to the same generic "Handoff" title. The **very first
> line** of your reply MUST name the actual work being saved (from `$ARGUMENTS` if given, else
> the session's main theme). Format:
>
> `Handoff — <specific topic of this session>`
>
> e.g. `Handoff — onboarding category-change cleanup + login-OTP fix`. Do NOT lead with generic
> "Handoff note" boilerplate.

## Gather state
This workspace has two git repos (root is not one). For **each** sub-repo that you touched
(`grassroots-laravel`, `grassroots-nextjs`), collect:

- Current branch — `git -C <repo> branch --show-current`
- Working-tree changes — `git -C <repo> status --short`
- Uncommitted diff summary — `git -C <repo> diff --stat`
- Recent commits this session — `git -C <repo> log --oneline -10`
- Whether those commits are pushed — `git -C <repo> rev-list --left-right --count origin/<branch>...<branch>`

Only include repos with relevant activity. Do not run dev servers or tests just to gather state.

Also note the **session transcript path** for the Record section below. Transcripts live at
`~/.claude/projects/<project-slug>/<session-id>.jsonl`; the session id is the directory name in
your own scratchpad path. Record it, never read it — they run to tens of megabytes.

## Write the note
Save to `.claude/handoffs/YYYY-MM-DD-HHmm-<kebab-title>.md` (use the real current date/time).
Use this template — be concrete and specific, no filler:

```markdown
# Handoff — <title>
_Date: <YYYY-MM-DD HH:mm> · Author: Claude_

## Goal
<What we set out to do this session, in 1–2 sentences.>

## Status
<Done / In progress / Blocked — one line.>

## What changed
- <repo>: <bullet list of concrete changes, with file links e.g. [file](path#Lnn)>

## Uncommitted / in-flight
- <Files with pending edits, why they are not finished. State "none — all committed" if clean.>

## Next steps
1. <The very next action, specific enough to start immediately.>
2. ...

## Decisions settled this session
- **<What was decided>** — <why, in the user's own reasoning where they gave it.>
  Rejected: <the alternative and why it lost, if one was weighed.>
- ...

## Still open
| Question | Blocks | Recommendation |
| --- | --- | --- |
| <the open question> | <what it stops, or "nothing"> | <the leaning, and why> |

## Gotchas & context
- <Dead ends, things to NOT redo, env/config caveats, API contract changes that still need a
  matching frontend/backend update. Link related memories with [[memory-name]].>

## Where the record lives
- Docs written or updated: <paths, so the next session reads those before the note>
- Artifacts published: <urls>
- Session transcript: `~/.claude/projects/<slug>/<session-id>.jsonl` (~NN MB).
  **Grep it, never read it whole.** Last resort only, for something this note failed to capture.

## How to verify
- <Exact commands: FE `cd grassroots-nextjs && npx tsc --noEmit -p tsconfig.json` + `npx eslint app components lib services`;
  BE `cd grassroots-laravel && vendor/bin/pint --dirty --format agent` + `php artisan test --compact`;
  the URL/endpoint to hit. Note: realtime needs Reverb WS + a fresh `queue:work`; tests run on
  sqlite so they do not catch MySQL column-width errors.>
```

### On the Decisions section
This is the part a future session cannot reconstruct from the code, and it is why the note
exists. Write the **reasoning**, not just the conclusion: a conclusion can be re-derived from
good reasoning, never the reverse. Record what the user settled explicitly so it is not
relitigated, and record what was rejected so the same dead end is not walked twice.

A long design session can settle dozens of things across many messages. If the volume is large,
put the detail in a doc under `grassroots-laravel/docs/` or `grassroots-nextjs/docs/` and let this
section carry the headlines plus the link. The note should stay readable in one sitting.

After writing, print the saved path and a 3-line summary. Do **not** commit anything unless asked.
