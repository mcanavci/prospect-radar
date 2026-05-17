# Wunder Radar — Wunder Mobility prospect intelligence agent

A multi-agent system that scans the global universe of shared mobility operators and produces a weekly ranked brief of operators showing **switcher signals** — public evidence they're at a re-platforming inflection point.

Built as a working artifact for my application to the AI-Native GTM Associate role at Wunder Mobility.

## What it does, plainly

Wunder sells to a small, knowable universe: roughly 100–150 paying shared-mobility operators worldwide. The hard part of GTM here isn't lead generation — every operator is findable. The hard part is **timing**. Operators don't re-platform on a clock; they do it when a specific event makes their current setup intolerable.

This system watches for those events.

For each operator in the universe it harvests five signal types:

| Signal | What it catches |
|---|---|
| **App store reviews** | Reliability complaints, downtime, broken flows — direct platform pain |
| **News & press** | Funding rounds, M&A, exec changes, expansion announcements |
| **Job postings** | "Platform engineer" / "fleet ops tooling" hires = build-vs-buy debate is live |
| **LinkedIn posts** | Founder pain posts, public technology complaints |
| **Outages & incidents** | Service interruptions, Downdetector spikes |

Each operator gets a score: **Pain × Timing × Wunder Fit**. The top 10 each week become a brief with a why-now hypothesis, the relevant Wunder proof point, and a draft first-touch message.

## Why this design

Three constraints shaped it.

**1. Wunder's TAM is small.** Doing the ACV math — $11.3M ARR ÷ ~€110K average contract ≈ ~100 operators — means the prospect universe is a list, not a search. The system maintains it as a versioned file ([data/operators.yaml](data/operators.yaml)), not a CRM query.

**2. The buying trigger is event-driven.** Most operators are happy enough on whatever they run today. The system watches for the events that *change* that — PE acquisitions, mergers, app-store collapses, model pivots — because those are the only moments where switching is on the table.

**3. AI-native means agentic, not "I prompt a chatbot."** Each signal type is its own agent with its own data source via MCP. They run in parallel. A scoring agent weighs the signals against a Wunder-specific rubric. A PVP agent writes the outreach hypothesis. This is the architecture Wunder describes building internally — same stack, applied to their commercial motion.

## What's in this folder

```
wunder-radar/
├── README.md                     this file
├── data/
│   └── operators.yaml            the universe (~80 operators with platform + signal flags)
├── scoring.md                    the rubric — how operators are scored and ranked
├── agents/                       Claude Code subagent definitions
│   ├── signal-news.md
│   ├── signal-jobs.md
│   ├── signal-reviews.md
│   ├── signal-linkedin.md
│   └── pvp-generator.md
├── prompts/
│   └── run.md                    the orchestrator prompt
├── output/
│   ├── 2026-05-12-top-10.md      this week's ranked brief — the killer artifact
│   └── briefs/                   per-operator deep dives
│       ├── nextbike.md
│       ├── snappcar.md
│       └── europcar-on-demand.md
├── run.py                        simple Python runner for adding new operators
└── loom-script.md                3-minute demo script
```

## How to run it

The system runs as a set of Claude Code subagents orchestrated by [prompts/run.md](prompts/run.md). To produce a fresh brief:

```
$ cd wunder-radar
$ claude
> /prompts/run.md
```

Or to score one new operator:

```
$ python run.py --operator "Free Now Drive"
```

Both paths produce a markdown brief in `output/`. The system uses Anthropic Claude (Sonnet for harvesting, Opus for scoring + PVP), MCP servers for App Store + news + jobs, and the Apify actors that already work for app-store reviews and LinkedIn posts.

## Defensibility notes

- Operator universe is built from public sources (Wunder customer pages, INVERS / Vulog / Joyride / ATOM case studies, EU mobility coverage, company press releases). Each entry has a `source` field.
- Platform attribution is graded `Confirmed` / `Inferred` / `Unknown`. ~30% of the universe is `Unknown` — that's the white space.
- Switcher signals are recorded with a URL. Anything I can't link, I don't claim.
- ACV estimates use Wunder's published pricing floor (€5K/mo Scale tier, €12.50–€36.50 per-vehicle) and assume the operator pays at the high end of their fleet bracket. Flagged as estimates.

## Open questions / where I'd improve this if I joined

- The "external GTM strategist" mentioned in the job ad — I'd want their scoring rubric and to fold it in.
- The scoring weights are my best guess. They should be tuned against your last 12 months of closed-won and closed-lost data.
- The Loom-recorded brief format is great for cold pipelines; for warm accounts (existing Wunder customers showing churn risk) the system needs a different harvester set.

— M. Can Avci, May 2026
