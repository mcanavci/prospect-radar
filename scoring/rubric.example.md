# Scoring rubric

Each prospect gets three scores (0–5). **Final score = Pain × Timing × Fit**, max 125.

The three dimensions are deliberately multiplicative, not additive. A prospect with 0 on any dimension is uninteresting regardless of the other two.

Adapt the anchors below to your specific market — the *categories* should stay (Pain, Timing, Fit) but the *signals* will differ.

---

## 1. Pain (0–5) — how much is their current state hurting them publicly?

Adapt to your market. The anchors below are from the Wunder Mobility example; replace with what "pain" looks like for *your* prospect's buying committee.

| Score | What it looks like (Wunder example) | Adapt to your market |
|-------|--------------------------------------|----------------------|
| **5** | Public founder complaints about platform, app store collapse (<2.5), confirmed multi-day outages | Public CEO statements admitting tooling failures; on-record complaints; security incidents |
| **4** | App store rating 2.5–3.0 with platform-blamed reviews, recent major incident, platform-related job-hunting | Customer churn announcements; Glassdoor reviews citing tooling pain; recent unplanned headcount adds |
| **3** | Mixed reviews, occasional reliability complaints, recent costly workaround | Recent emergency hiring; consulting engagements visible publicly; growing internal team to compensate |
| **2** | App rating 3.0–3.5, no acute pain but signs of strain | Quiet but with a single weak signal |
| **1** | Smooth operation, no public complaints | Steady state |
| **0** | They ARE the vendor in your category (out of scope) | N/A |

## 2. Timing (0–5) — how likely is a buying decision in the next 6 months?

Timing scores the EVENT that's unlocked their normally-frozen procurement.

| Score | Trigger (universal) | Specific examples (Wunder) |
|-------|---------------------|----------------------------|
| **5** | M&A closed in last 12 months; new CEO in last 6 months; pre-launch | PE acquisition, merger, founder return |
| **4** | Funding round in last 12 months; major model pivot; expansion to a third market | Series B/C/extension; free-float → station |
| **3** | New CTO/VP Engineering in last 12 months; multiple senior platform hires posted | Engineering org rebuild signals |
| **2** | Quiet but with one weak signal (e.g. a single platform-related job post) | Single relevant job posting |
| **1** | Steady state | Routine quarterly updates |
| **0** | No signals at all | Dormant account |

## 3. Fit (0–5) — how well does YOUR capability map to what they need?

Fit is the dimension most often gamed. Be honest. A prospect that doesn't match your sweet spot at 5 is a 5 on *aspiration*, not Fit. Lower it.

| Score | Pattern (Wunder example) | How to evaluate |
|-------|--------------------------|-----------------|
| **5** | Multi-modal operator, 500+ vehicles, Europe | Maps cleanly to your strongest pricing tier + at least one named customer reference |
| **4** | Mid-large operator, single-modality, white-label appetite | Fits cleanly but you may not have the exact proof point at scale |
| **3** | Mid-sized operator in geography you serve | Reasonable fit; would need to compete on price/features |
| **2** | Below pricing floor; competitor product is a better fit | You'd win by accident, not design |
| **1** | Geography or modality you have no proof points in | High loss-risk |
| **0** | Vertically integrated; will never buy externally | Disqualify |

---

## Final score = Pain × Timing × Fit

Maximum 125. Tiebreakers:

1. ACV estimate (higher wins)
2. Geography overlap with existing customer references (more overlap wins)
3. Modality / segment overlap (more overlap wins)

---

## Weights (tune against closed-won data)

The three dimensions default to equal weight (pure product). If your closed-won data shows a pattern, re-weight:

- If most won deals had a re-platforming **trigger** → weight Timing higher (multiplier 1.5)
- If most won deals were **broken existing tooling** → weight Pain higher (multiplier 1.5)
- If most won deals were **"this is exactly the product shape we need"** → weight Fit higher (multiplier 1.5)

`run.py` reads weights from the `scoring.weights` block in `config/target.yaml` if you set them. Default is `{pain: 1.0, timing: 1.0, fit: 1.0}`.

---

## What this rubric deliberately does NOT score

- **Buyer persona accessibility** — that's an outreach concern, not a fit concern, and you don't want to bias toward prospects with loud founders.
- **Sales cycle length estimate** — too noisy to score.
- **Current vendor identity** — beyond Confirmed/Inferred/Unknown, don't favor stealing from a specific competitor.

---

## How to test your rubric

Before going live, score 10 of your **closed-won** accounts as if they were still prospects (use signals from the period before they signed). You should see them score in the top quartile. If they don't, the rubric needs anchor adjustment, not weight adjustment.

---

# The Pain Stack (v2)

The v1 scoring above uses surface signals: news, jobs, reviews, LinkedIn. These are easy to harvest and easy for anyone to fake. **Pain Stack v2 is the depth layer** — market-specific signals that require domain knowledge to even know where to look.

## The hypothesis

**Single weak signals are noise. Stacked, they segment the universe by shape of pain, not firmographics.**

A prospect with one mediocre signal looks like everyone else. The same prospect with three weak-but-independent signals from different sources is in *strategic crisis*, not just operational pain — and strategic crisis is when buying decisions that are normally frozen get re-opened.

## How stacking works

The `signal-deep-market` agent reads the `deep_signals` block from `config/target.yaml` and checks each signal per prospect. Output is a hit count.

| Hits | Tag | Effect on rank |
|------|-----|----------------|
| **3+** | `Pain Stack: HIT` | 1.3× multiplier on final score + auto-tag in brief |
| **1–2** | `Pain Stack: PARTIAL` | Flagged in brief, no multiplier |
| **0** | `Pain Stack: MISS` | No effect; v1 score stands |

The 1.3× multiplier is configurable in `target.yaml > scoring.pain_stack_multiplier`. Default 1.3.

## Example (Wunder)

Three v2 signals defined for Wunder:

1. **City permit cycle** — operator within 12 months of permit renewal AND complaints in council minutes
2. **Review trajectory falling** — rating dropped 0.5+ in last 90 days
3. **Fleet count gap** — claimed vs deployed fleet diverging 20%+

The stack:

```
Permit renewal in <12 months  +  Trustpilot falling 0.5+ in 90 days  +  Fleet count gap widening
                                              ↓
                              Pain Stack: HIT — strategic crisis
                                              ↓
                                  1.3× score multiplier
```

A prospect that misses ALL v1 signals but hits the Pain Stack still scores high enough to surface — exactly the kind of account you'd otherwise miss because they're "quiet" on the surface.

## When v1 and Pain Stack disagree

Two interesting failure modes the brief should call out:

- **v1 strong, Pain Stack miss:** "The market is talking about them, but the structural pain isn't there. May be operational noise, not a buying signal."
- **v1 weak, Pain Stack hit:** "Public surface is quiet, but the structural pain is real. These accounts are often the best targets because no competitor is messaging them."

The brief should surface both modes explicitly so the seller doesn't dismiss the second category by default.

## Designing your Pain Stack

Read [docs/designing-pain-stack-signals.md](../docs/designing-pain-stack-signals.md). The short version: pick 3-5 signals where (a) the data is public, (b) the data is hard to fake, and (c) the signal has a temporal dimension (renewal cycle, trajectory, drift) — pure point-in-time facts don't stack.
