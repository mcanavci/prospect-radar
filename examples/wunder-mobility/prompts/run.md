# Wunder Radar — orchestration prompt

Run this in Claude Code from the project root. It triggers the full signal harvest and produces a fresh top-10 brief in `output/`.

---

You are running Wunder Radar for Wunder Mobility. Your job: produce a fresh weekly brief of the top 10 shared-mobility operators most likely to re-platform in the next 6 months.

## Steps

1. **Load the operator universe**
   Read [data/operators.yaml](../data/operators.yaml). This is the list of operators to scan. Filter to operators with `wunder_fit >= 3` AND `status: Active`.

2. **Harvest signals (parallel)**
   For each operator passing the filter, launch four signal-harvester subagents in parallel:
   - `signal-news`
   - `signal-jobs`
   - `signal-reviews`
   - `signal-linkedin`
   Each returns a YAML block with scored signals.

3. **Score**
   Combine the harvest scores with the static `wunder_fit` from operators.yaml.
   Final score = (Pain × Timing × Fit), where:
   - **Pain** = max(reviews_score, jobs_pain_score)
   - **Timing** = max(news_score / 5, jobs_buy_score / 5, linkedin_score / 5) capped at 5
   - **Fit** = wunder_fit from operators.yaml

4. **Rank**
   Sort by final score descending. Break ties using ACV potential (use fleet_est × Wunder pricing floor as proxy).

5. **Generate PVP for top 10**
   For each of the top 10, invoke `pvp-generator` with the harvested signals + Wunder reference list. Each output must pass the 3 WHYs audit before inclusion.

6. **Compile the brief**
   Output to `output/YYYY-MM-DD-top-10.md` in the same format as [output/2026-05-12-top-10.md](../output/2026-05-12-top-10.md). Sections:
   - Header (date, headline summary of 3-4 themes)
   - 10 operator entries with score breakdown, signals, draft message
   - Summary table by play type
   - "What I'd do Monday morning" — concrete action list

7. **Generate per-operator deep-dives for top 3**
   Write full account briefs to `output/briefs/[operator-slug].md` for the top 3 ranked operators. Format mirrors [output/briefs/europcar-on-demand.md](../output/briefs/europcar-on-demand.md).

## Quality rules

- Every claim has a source link or is flagged as inferred.
- ACV estimates are bands, not point numbers, and reference published Wunder pricing.
- Anything below score 15 is excluded from the top 10 even if it has rank 1–10 — quality bar over slot-filling.
- If a signal harvester fails (insufficient public data), exclude that signal from scoring rather than fabricating.
- The brief is for an AE Monday morning. Write like a senior operator wrote it for themselves.

## How to add a new operator mid-run

If during signal harvest you discover an operator not in `data/operators.yaml`, append it to the YAML file in the appropriate segment block with `platform_confidence: Unknown` and run the harvest on it. Add a note in the brief: "Universe expanded by 1 operator this run."

## Failure modes to watch for

- **News agent over-counts**: many operators get coverage simply because they're large. Score the SIGNAL TYPE, not the number of articles.
- **App store agent samples wrong app**: white-label operators run the underlying platform's app, not their own. Verify the app name before scraping.
- **LinkedIn agent fabricates posts**: LinkedIn is hard to scrape. If the agent can't link a post, reject it.
- **PVP generator writes generic messages**: re-run with stricter instructions if the 3 WHYs audit fails.
