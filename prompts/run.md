# prospect-radar — orchestration prompt

Run this in Claude Code from the project root. It triggers the full signal harvest and produces a fresh top-10 brief in `output/`.

---

You are running prospect-radar.

## Setup (read these files into context)

1. **Target config** — read `config/target.yaml`. This is the company doing the selling.
2. **Universe** — read `data/universe.yaml`. This is the list of prospects to scan.
3. **Scoring rubric** — read `scoring/rubric.md`. This defines how prospects are ranked.

## Steps

1. **Filter the universe.** Keep only prospects with `fit_score >= 3` AND `status: Active`.

2. **Harvest v1 signals (parallel).** For each filtered prospect, launch four signal-harvester subagents in parallel:
   - `signal-news`
   - `signal-jobs`
   - `signal-reviews` (includes trajectory mode — captures Δrating over 90 days)
   - `signal-linkedin`

   Each returns a YAML block with scored signals.

3. **Harvest v2 Pain Stack signals (if configured).** If `config/target.yaml` has a non-empty `deep_signals` block, launch `signal-deep-market` for each prospect. It checks each user-defined deep signal and returns a hit count.

   If `deep_signals` is absent, skip this step entirely — v1 only.

4. **Compute base score.** For each prospect:
   - **Pain** = max(reviews.pain_score, jobs.operate_score)
   - **Timing** = max(news.total_news_score / 5, jobs.build_score, linkedin.total_linkedin_score / 3) capped at 5
   - **Fit** = fit_score from `data/universe.yaml`
   - **Base total** = Pain × Timing × Fit (max 125)

   Apply weights from `config/target.yaml > scoring.weights` if defined; default `{pain: 1.0, timing: 1.0, fit: 1.0}`.

5. **Apply Pain Stack multiplier.** Read each prospect's `pain_stack_status` from the deep-market harvester output:
   - `hit` (3+ deep signals fired) → final = base × multiplier (default 1.3, configurable via `target.yaml > scoring.pain_stack_multiplier`). Tag the prospect `Pain Stack: HIT`.
   - `partial` (1-2 deep signals fired) → no multiplier. Tag `Pain Stack: PARTIAL`. Mention in the brief.
   - `miss` (0 deep signals fired) → no effect. Tag silently.

6. **Rank.** Sort by final score descending. Break ties using ACV estimate from `config/target.yaml > pricing` × estimated account size.

   In the ranked output, explicitly surface two disagreement cases:
   - **v1 strong + Pain Stack miss** — flag "operational, not structural"
   - **v1 weak + Pain Stack hit** — flag "structurally stressed, surface-quiet" (often the best targets)

7. **Generate PVP for top 10.** For each top-10 prospect, invoke `pvp-generator` with the harvested signals + target's proof points. Each output must pass the 3 WHYs audit before inclusion.

8. **Generate per-prospect deep-dives for top 3.** Write full account briefs to `output/briefs/[slug].md`. Each brief includes:
   - Snapshot
   - Why now (timing signals)
   - The pain dossier (with Pain Stack tag if HIT)
   - Specific fit
   - Three-message sequence (first touch + 2 follow-ups)
   - Discovery questions for first call
   - Risks / disqualifiers
   - Confidence + reasoning
   - Sources

9. **Compile the weekly brief.** Output to `output/YYYY-MM-DD-top-10.md`. Sections:
   - Header (date, headline summary of 3–4 themes from the week)
   - 10 prospect entries with score breakdown, Pain Stack status, signals, draft message
   - Pain Stack callout block: HITs and PARTIALs, with the "v1 strong / v2 miss" and "v1 weak / v2 hit" disagreement cases
   - Summary table by play type
   - "What I'd do Monday morning" — concrete action list

## Quality rules

- Every claim has a source link or is flagged as inferred.
- ACV estimates are bands, not point numbers, and reference the target's published pricing.
- Anything below score 15 is excluded from the top 10 even if it ranks 1–10 — quality bar over slot-filling.
- If a signal harvester returns `status: insufficient_*` for a prospect, exclude that signal from scoring (don't substitute zero — score with remaining signals using max() logic).
- Deep-signal "hits" without working URLs are not hits.
- The brief is for a seller's Monday morning. Write like a senior operator wrote it for themselves.

## How to add a new prospect mid-run

If during signal harvest you discover a prospect not in `data/universe.yaml`, append it with `platform_confidence: Unknown` and run the harvest on it. Add a note in the brief: "Universe expanded by 1 prospect this run."

## Failure modes to watch for

- **News agent over-counts:** many prospects get coverage simply because they're large. Score the SIGNAL TYPE, not the article count.
- **Reviews agent samples wrong product:** white-label customers run the underlying platform's app, not their own. Verify the product surface before scoring.
- **LinkedIn agent fabricates posts:** LinkedIn is hard to scrape. If the agent can't link a post, reject it.
- **PVP generator writes generic messages:** re-run with stricter instructions if the 3 WHYs audit fails twice.
- **Deep-market agent fabricates "hits":** if a source is unreachable, report `unreachable` — never substitute "no hit" silently.
