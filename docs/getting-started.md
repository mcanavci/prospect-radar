# Getting started

A 15-minute walkthrough to your first brief.

## Prerequisites

- Python 3.11+
- An [Anthropic API key](https://console.anthropic.com/settings/keys)
- A list of 20+ prospects you sell to (the "knowable TAM")
- One existing customer with public proof you can reference

If you don't have these yet, prospect-radar isn't the right tool — go fix that first.

## Step 1 — Clone and install

```bash
git clone https://github.com/<you>/prospect-radar
cd prospect-radar
pip install anthropic pyyaml
export ANTHROPIC_API_KEY=sk-ant-...
```

## Step 2 — Define your target company

```bash
cp config/target.example.yaml config/target.yaml
$EDITOR config/target.yaml
```

The most important sections to get right on first pass:

- **`company.short_description`** — one paragraph. The agents inject this into every harvest. Be specific about what you sell.
- **`icp`** — who you sell to. Tighter is better.
- **`proof_points`** — at least 2-3 real customers with one-line use-cases. The PVP generator can't work without these.
- **`pricing`** — used to estimate ACV per prospect. Even rough numbers help.
- **`buyers`** — what each persona cares about. Drives message tone.

If you only have 30 minutes for this step, that's fine — you'll iterate after seeing the first brief.

## Step 3 — Build your universe

```bash
cp data/universe.example.yaml data/universe.yaml
$EDITOR data/universe.yaml
```

Source your prospects from:

- Your competitors' customer pages (their wins = your potential wins)
- Industry analyst reports / market maps
- Recent funding announcements in your category
- Conference attendee lists
- Your own deal history (closed-lost is often the best pipeline)

Aim for 30–100 entries on first build. You can grow it from there.

Each prospect needs at least: `name`, `hq`, `segment`, `fit_score` (your gut score 0-5), and `status: Active`.

## Step 4 — (Optional) Tune the scoring rubric

```bash
cp scoring/rubric.example.md scoring/rubric.md
$EDITOR scoring/rubric.md
```

The defaults work as starting point. Don't tune weights until you have at least one weekly run done — you'll see which dimension is actually predictive in your market.

## Step 5 — Run your first brief

```bash
python run.py --prospect "Some Account I Know Well"
```

Watch the output. Expect 60-120 seconds (the model calls web_search several times).

Output lands at `output/briefs/some-account-i-know-well.md`.

Read it critically:

- Is the pain hypothesis right?
- Are the signals real (click every URL)?
- Does the draft message pass the 3 WHYs audit *for you*, not just the auditor?
- Would you actually send this Monday?

If yes, you've got a working radar.

## Step 6 — Generate the weekly top-10

Open Claude Code in the project root, then:

```
> /prompts/run.md
```

This kicks off the full universe sweep. Expect 5-15 minutes for a 50-prospect universe.

Output: `output/YYYY-MM-DD-top-10.md` + per-prospect briefs for the top 3 in `output/briefs/`.

## Step 7 — Calibrate

The first brief is rarely perfect. The usual fixes:

- **Pain signals are weak** → reviews are scoring noise, not platform pain → tighten the `signal-reviews` agent or move the rubric anchor stricter
- **Proof points don't land** → your `proof_points` block in `target.yaml` is too generic → add specific scale + outcome per customer
- **PVP messages sound templated** → the 3 WHYs are passing but the messages feel hollow → review `agents/pvp-generator.md` style rules, add your own anti-patterns

## What "done" looks like

You're done with setup when:

1. You can run `python run.py --prospect "X"` and get a brief you'd send
2. The weekly top-10 produces 3+ accounts you didn't already know were ripe
3. You ship the radar's first cold outbound and get a reply within a week

That's the bar. Everything else is iteration.
