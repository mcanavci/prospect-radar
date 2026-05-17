---
description: "Run prospect-radar. Usage: /radar <prospect-name> | /radar weekly | /radar help"
argument-hint: "<prospect-name> | weekly | help"
---

You are running prospect-radar — multi-agent prospect intelligence for B2B SaaS with knowable TAMs.

## Step 0 — Locate the instance

This command is project-scoped. It expects to run from inside a prospect-radar instance — a directory containing `config/target.yaml`, `data/universe.yaml`, `scoring/rubric.md`, and `agents/*.md`.

Check the current working directory:

```bash
ls config/target.yaml data/universe.yaml scoring/rubric.md
```

If any are missing, tell the user:

> Not in a configured prospect-radar instance. Make sure you're in the right directory and that you've copied the .example files:
>
> ```
> cp config/target.example.yaml config/target.yaml
> cp data/universe.example.yaml data/universe.yaml
> cp scoring/rubric.example.md scoring/rubric.md
> ```
>
> Then edit each file to match your target company and prospect universe. See docs/getting-started.md for the walkthrough.

## Step 1 — Parse the argument

`$ARGUMENTS` will be one of:

- **A prospect name** (e.g. `"Acme Corp"`) → single-prospect brief
- **`weekly`** → full universe sweep, top-10 brief
- **`help`** or empty → print usage

## Step 2A — Single-prospect brief

If `$ARGUMENTS` is a prospect name:

1. Load `config/target.yaml`, `data/universe.yaml`, `scoring/rubric.md`, and all `agents/*.md` into context
2. Check if the prospect exists in `data/universe.yaml`. If yes, use the static record as context.
3. Launch 4 v1 signal harvesters in parallel: `signal-news`, `signal-jobs`, `signal-reviews` (with trajectory mode), `signal-linkedin`
4. If `config/target.yaml > deep_signals` is non-empty, also launch `signal-deep-market`
5. Compute final score: (Pain × Timing × Fit) × Pain Stack multiplier
6. Invoke `pvp-generator` with the harvested signals + target's proof points
7. Output the brief in the format from `prompts/run.md` step 8 (per-prospect deep-dive)
8. Write to `output/briefs/<slug>.md`
9. Print a short summary in chat (score, Pain Stack tag, why-now in 1 line, first-touch CTA) + the path to the written file

## Step 2B — Weekly sweep

If `$ARGUMENTS` is `weekly`:

1. Load instance configs (same as 2A step 1)
2. Filter universe: `fit_score >= 3` AND `status: Active`
3. For each prospect, run the harvest + score pipeline from Step 2A — concurrent (max 5 at a time to avoid rate limits)
4. Rank by final score; surface "v1 strong / v2 miss" and "v1 weak / v2 hit" cases in the brief
5. Generate PVP for top 10
6. Write per-prospect deep-dives for top 3 to `output/briefs/`
7. Write top-10 brief to `output/<YYYY-MM-DD>-top-10.md`
8. Print summary in chat: top 5 names + scores + Pain Stack status + path to full brief

## Step 2C — Help

If `$ARGUMENTS` is `help` or empty:

```
prospect-radar — multi-agent prospect intelligence

Usage:
  /radar "Acme Corp"     Brief one prospect (uses cwd's target.yaml)
  /radar weekly          Run full universe sweep, produce top-10 brief
  /radar help            This message

Run from inside a prospect-radar instance directory. To set up a new
instance, see docs/getting-started.md.

Repo: https://github.com/mcanavci/prospect-radar
Wunder example: https://wunder-radar.vercel.app
```

## Hard rules

- **Never fabricate signals.** If a source is unreachable, mark it `unreachable` — don't invent.
- **Every claim has a source URL.** Unlinkable claims get dropped.
- **3 WHYs audit on every draft message.** If it fails, mark `ready_to_send: no`.
- **No em-dashes in output messages.** Use periods, commas, or "to" in year ranges.
- **The brief is for a seller's Monday morning.** Write like a senior operator, not a marketing intern.

## What to write to chat vs file

**Single-prospect (2A):**
- Chat: brief summary (score, Pain Stack tag, why-now in 1 line, first-touch CTA), then optionally the full brief inline
- File: full brief at `output/briefs/<slug>.md`

**Weekly (2B):**
- Chat: top 5 rows summary table + path to full brief
- File: top-10 at `output/<YYYY-MM-DD>-top-10.md` + top-3 deep dives at `output/briefs/`
