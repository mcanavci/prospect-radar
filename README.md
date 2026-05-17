# prospect-radar

A multi-agent system that watches your prospect universe for **switching signals** — public evidence that an account is at a buying inflection point — and produces a weekly ranked brief with ready-to-send first-touch messages.

Built for B2B SaaS sellers with a **knowable TAM** (think: vertical SaaS, infrastructure, devtools, regulated software). The hard part of GTM at this scale isn't finding accounts. It's timing.

```
Universe (your knowable TAM)
  ↓
v1 — Surface signals (parallel harvest, breadth)
  ├── News (M&A, funding, exec changes, expansion)
  ├── Jobs (build-vs-buy signals)
  ├── App store reviews (product-blamed UX pain) + trajectory
  ├── LinkedIn posts (founder pain, public complaints)
  └── Outages / incidents
  ↓
v2 — Pain Stack (depth signals, user-defined per market)
  e.g. permit renewal cycles, regulatory deadlines, capacity gaps,
       transaction velocity, agent migration — whatever's domain-specific
  ↓
Scoring (Pain × Timing × Fit) × Pain Stack multiplier
  ↓
PVP generator (one ready-to-send message per top prospect)
  ↓
Weekly brief (top N + per-prospect deep-dives + Pain Stack callouts)
```

## Why this exists

Most outbound tooling answers "who do I email?" with bigger and bigger lists. That's the wrong question when your TAM is small and knowable. The right question is **when** to email each account — and **what specific pain** they're feeling this week.

`prospect-radar` answers that. You define your prospect universe once. Every week, the system harvests public signals against every account, scores them, and ranks the top N most likely to buy in the next 6 months. For each, it generates a draft outbound message tied to the specific pain that surfaced.

## v1 vs v2 — the Pain Stack

**v1 signals** (news, jobs, reviews, LinkedIn) are surface signals. Anyone can find them. They tell you "this account is on the move."

**v2 — the Pain Stack** — are depth signals defined per market. They require domain expertise to know they exist. They tell you "this account is in *strategic crisis*, even if the surface looks fine."

Examples of Pain Stack signals by market:
- **Shared mobility:** city permit renewals, fleet count gaps (claimed vs deployed), review trajectory direction
- **Football SaaS:** transfer window cycles, FFP deadlines, league position deltas, sporting director churn
- **Compliance SaaS:** open enforcement actions, audit cycles, regulator hires
- **Real estate platforms:** permit databases, transaction velocity, agent migration

**The hypothesis:** single weak signals are noise. Stacked, they segment the universe by shape of pain, not firmographics. A prospect that's quiet on v1 but hits 3 v2 signals is often the strongest target — because no competitor is messaging them.

Define your Pain Stack in `config/target.yaml > deep_signals`. The radar will check each, count hits, and multiply the final score for any prospect that scores ≥3 hits. See [docs/designing-pain-stack-signals.md](docs/designing-pain-stack-signals.md).

## Live example

[`examples/wunder-mobility/`](examples/wunder-mobility/) is the canonical reference — a complete instance built for [Wunder Mobility](https://wundermobility.com) (Hamburg-based shared-mobility SaaS). 53 operators scored, top 10 ranked, three deep-dive briefs with first-touch messages, $3.7M–$8M weighted pipeline against $11.3M ARR.

Live one-pager: [wunder-radar.vercel.app](https://wunder-radar.vercel.app)

## Setup — for your own target company

```bash
# 1. Clone the repo as a new instance for your target company
git clone https://github.com/mcanavci/prospect-radar my-company-radar
cd my-company-radar

# 2. Configure your company (the one selling)
cp config/target.example.yaml config/target.yaml
$EDITOR config/target.yaml

# 3. Build your prospect universe
cp data/universe.example.yaml data/universe.yaml
$EDITOR data/universe.yaml

# 4. Tune the scoring rubric for your market
cp scoring/rubric.example.md scoring/rubric.md
$EDITOR scoring/rubric.md

# 5. Install deps + set API key
pip install anthropic pyyaml
export ANTHROPIC_API_KEY=sk-ant-...
```

A new instance takes a focused user ~half a day to set up. The technical part is fast; the work is in `target.yaml` and `universe.yaml` — that's where your GTM thinking lands.

## Daily usage — two interfaces

**Option A: Claude Code slash command (recommended for daily use)**

From inside your radar instance directory:

```
/radar "Acme Corp"     # Ad-hoc brief for one prospect
/radar weekly          # Full universe sweep, top-10 brief
/radar help            # Usage
```

The slash command ships with the repo at `.claude/commands/radar.md` — Claude Code picks it up automatically when you open the project.

**Option B: Python CLI (for cron / non-Claude-Code workflows)**

```bash
python run.py --prospect "Acme Corp"           # Ad-hoc brief
python run.py --prospect "New Co" --add-to-universe  # Brief + append to universe
```

The weekly sweep currently runs via `/prompts/run.md` in Claude Code. You can wire it to cron by wrapping it in your own shell script that invokes `claude` headlessly.

## (Optional) Deploy a public one-pager

```bash
npm install -g vercel
vercel deploy
```

See [docs/deploying-to-vercel.md](docs/deploying-to-vercel.md) for the details + privacy tradeoffs (public deploy vs password-protected).

## What goes in each file

| File | What it defines |
|------|-----------------|
| `config/target.yaml` | YOUR company — name, product, ICP, pricing tiers, customer proof points, buyer personas |
| `data/universe.yaml` | THE PROSPECTS — your knowable TAM as a versioned list. ~50–500 entries depending on market. |
| `scoring/rubric.md` | The Pain × Timing × Fit anchors, with per-market interpretations. Tune weights against your closed-won data. |
| `agents/*.md` | Signal harvesters. Mostly portable. |
| `prompts/run.md` | The orchestrator. Triggers the weekly run from Claude Code. |
| `run.py` | Python runner. Loads config, runs the pipeline, writes briefs. Uses Anthropic SDK with prompt caching. |
| `output/` | Where the weekly brief and per-prospect deep-dives land. Gitignored by default. |

## When this works well

- Your TAM is **knowable** (100–1,000 accounts, not 100,000)
- Buying decisions are **event-triggered** (M&A, exec changes, public pain, model pivots)
- Your prospects produce **public signals** (press, jobs, app stores, LinkedIn)
- You sell **mid-market or enterprise** ACV (the per-account cost of monitoring is worth it)

## When this doesn't work

- TAM is huge and undifferentiated (use intent data / 6sense / clearbit reveal instead)
- Buying decisions are routine renewals or low-touch self-serve
- Your prospects are too small for public press / job posts / app stores
- You don't have customer proof points yet (the PVP generator needs reference accounts)

## Architecture notes

- **Signal harvesters run in parallel** as Claude subagents — defined in `agents/` as plain markdown
- **Scoring is deterministic**, not LLM-judged — the rubric in `scoring/rubric.md` is the source of truth
- **PVP messages are LLM-generated** but every one passes a [3 WHYs audit](docs/writing-a-target-config.md#the-3-whys-audit) before being marked "ready to send"
- **State lives in YAML files**, not a database — diffable in git, editable in your editor
- **The Python runner uses prompt caching** to amortize the cost of re-reading the universe / rubric / agent specs across runs

## Docs

- [Getting started](docs/getting-started.md) — 15-minute setup walkthrough
- [Writing a target config](docs/writing-a-target-config.md) — how to populate `target.yaml` well
- [Tuning scoring weights](docs/tuning-scoring-weights.md) — using your closed-won data to calibrate
- [Deploying to Vercel](docs/deploying-to-vercel.md) — turning your output into a public one-pager

## License

MIT. Fork it, customize it, ship it.

## Built by

[M. Can Avci](https://mcanavci.github.io). The original instance was [Wunder Radar](https://wunder-radar.vercel.app) — a working artifact built over a weekend for the Wunder Mobility AI-Native GTM Associate application.
