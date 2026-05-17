# Scoring rubric

Each operator gets three scores, each 0–5. Total = product, max 125.

## 1. Pain (0–5)

How much is their current platform hurting them publicly?

| Score | What it looks like |
|---|---|
| **5** | Public founder complaints about platform, app store collapse (rating <2.5), confirmed multi-day outages, lost a major city contract over platform issues |
| **4** | App store rating 2.5–3.0 with platform-blamed reviews, recent major incident, employees publicly job-hunting on platform-related grounds |
| **3** | Mixed reviews, occasional reliability complaints, recent costly workaround required (e.g. emergency hires) |
| **2** | App rating 3.0–3.5, no acute pain but signs of strain |
| **1** | Smooth operation, no public complaints |
| **0** | They are the platform vendor (Lime, Voi, Bolt) — N/A |

## 2. Timing (0–5)

How likely is a re-platforming decision in the next 6 months?

| Score | Trigger |
|---|---|
| **5** | M&A closed in last 12 months (PE acquisition, merger), or new CEO in last 6 months, or pre-launch (net-new) |
| **4** | Funding round closed in last 12 months, or major model pivot announced (e.g. free-float → station), or expansion into a third country |
| **3** | New CTO/VP Engineering in last 12 months, multiple senior platform/tooling hires posted |
| **2** | Quiet but with one weak signal (e.g. a single platform-related job post) |
| **1** | Steady state |
| **0** | No signals at all |

## 3. Wunder Fit (0–5)

How well does Wunder's actual capability map to what they need?

| Score | Pattern |
|---|---|
| **5** | Multi-modal operator (bikes + scooters + mopeds + cars under one brand) — Wunder's cross-operator marketplace + multi-modal platform is direct fit |
| **4** | Mid-to-large operator (500+ vehicles), Europe-based, white-label appetite, station-based or free-float — fits the Scale tier cleanly |
| **3** | Mid-sized operator, single modality, geography Wunder already serves |
| **2** | Small operator (under 400 vehicles) — below Wunder's pricing floor; ATOM/Joyride fit better |
| **1** | Geography or modality Wunder has no proof points in |
| **0** | Vertically integrated giant — Wunder loses on principle (Lime, Meituan, Bolt) |

## Score example

**Nextbike** post-PE-acquisition + rebrand:
- Pain: 2 (no acute public pain but PE owners scrutinize cost structure)
- Timing: 5 (PE acquisition May 2024 + "comprehensive rebrand" announced)
- Wunder Fit: 5 (115K bikes across 300+ cities, the biggest fish; Wunder's Forest case study is the proof point)
- **Total: 2 × 5 × 5 = 50**

## Tiebreakers (used at the same total score)

1. ACV estimate (higher wins)
2. Geography overlap with existing Wunder references
3. Cross-operator marketplace fit (would they benefit from Wunder's GreenMobility/Cooltra/Emmy rider pool)

## What this rubric deliberately does not score

- **Buyer persona accessibility** (founder reachability on LinkedIn etc.) — that's an outreach concern, not a fit concern, and we don't want to bias toward operators with loud founders.
- **Sales cycle length estimate** — too noisy to score, and the AI-native motion should compress it for all of them anyway.
- **Current platform vendor identity** — beyond Confirmed/Inferred/Unknown, we don't favor stealing from a specific competitor.

## Weights (tunable)

Default is equal weight across the three. The right weights depend on Wunder's closed-won data:
- If most won deals had a re-platforming trigger event → weight Timing higher
- If most won deals were existing-platform-was-broken → weight Pain higher
- If most won deals were "we needed a multi-modal platform" → weight Fit higher

These should be tuned in the first 90 days using Wunder's actual deal data, not assumed.
