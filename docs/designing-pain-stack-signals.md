# Designing your Pain Stack

v1 signals (news, jobs, reviews, LinkedIn) are universal — they work for every B2B SaaS. **v2 Pain Stack signals are market-specific** and require you to know your domain. They're what separates a brief that anyone could produce from a brief that proves you understand your prospect's world.

This doc helps you design 3-5 signals for your market.

## The hypothesis (restated)

Single weak signals are noise. Three or more weak signals from independent sources, stacked on the same prospect, indicate **strategic crisis** — buying decisions get re-opened.

## What makes a good Pain Stack signal

A v2 signal qualifies if **all four** are true:

**1. The data is public.** No paid data sources, no scraping behind logins. If a competitor can't reproduce the harvest in 30 minutes, the signal isn't durable.

**2. The data is hard to fake.** Press releases are easy to manipulate. Permit databases, court records, regulatory filings, public infrastructure data — these are hard to fake. Bias toward the hard-to-fake.

**3. The signal has a temporal dimension.** Point-in-time facts don't stack. Look for cycles, deadlines, trajectories, drifts. "Permit expires in 8 months" stacks; "permit exists" doesn't.

**4. The signal correlates with buying decisions in your market.** This is the test most signals fail. A signal that's intellectually interesting but doesn't predict closes is wasted.

## How to find signals for your market

### Step 1 — Reverse-engineer your closed-won deals

Take your last 10 won deals. For each, ask: **"What public event preceded their decision to evaluate us?"**

Possible answers per market:

- They got acquired → check M&A databases, PE press
- They lost a city contract → check city tender outcomes, council minutes
- They hired a new CTO → exec change databases
- Their compliance deadline approached → regulatory calendars
- A competitor failed publicly → press / outages
- A funding round closed → Crunchbase / Pitchbook
- Their fleet/team grew faster than their tooling → job postings, headcount data
- A senior person left to a competitor → LinkedIn moves

Sort by frequency. The top 3-5 are your candidate signals.

### Step 2 — Find the data source

For each candidate, identify the **specific public data source** that surfaces it. Concrete examples:

| Signal type | Data source examples |
|------------|----------------------|
| Public procurement / city contracts | EU TED API, USA SAM.gov, UK Contracts Finder |
| Permits / licenses | City open-data portals (NYC, London, Berlin, Madrid have rich ones) |
| Council / board minutes | Municipal council websites, often searchable |
| Regulatory enforcement | SEC, FCA, BaFin, FINMA filings |
| Court records | PACER (US), public court databases |
| Patent filings | Google Patents, EPO, USPTO |
| Corporate filings | Companies House (UK), SEC EDGAR (US), Bundesanzeiger (DE) |
| Fleet / deployment data | City open-data, transit authority APIs |
| Pricing / public listings | Their own pricing page, archived via Wayback Machine for trajectory |
| Job postings (deeper) | Internal recruiter LinkedIn, not just company careers page |

If you can't find a public source for a candidate signal, **drop it.** The signal needs to be queryable.

### Step 3 — Define `hit_criteria`

Each signal needs a concrete rule for when it fires. Vague criteria produce vague hits.

**Bad:** "Recent enforcement action"
**Good:** "Open enforcement action with regulator XYZ in last 12 months, status 'active' or 'pending'"

**Bad:** "Reviews are bad"
**Good:** "Rating dropped 0.5+ points in the last 90 days vs the prior 90 days"

**Bad:** "Permits are expiring"
**Good:** "Operating permit expires within 12 months AND council minutes from last 90 days mention rider/customer complaints"

Write the criteria so a human (or LLM) could check it in 5 minutes.

### Step 4 — Set weights

Each signal in `config/target.yaml > deep_signals` has a `weight` field (1-3). Weight reflects:

- How strong the signal is alone
- How exclusive the signal is (rare signals → higher weight)
- How predictive in your historical data

Default to weight `2` and adjust after you see the first month of briefs.

### Step 5 — Test on closed-lost

After scoring your closed-won deals, test the signals on your **closed-lost** deals from the same period. If your Pain Stack would have promoted those losses to "HIT" — that's a false positive. The signals need recalibration.

The right outcome: closed-won has more Pain Stack HITs than closed-lost. If they're equal, the stack isn't predictive in your market.

## Market-specific examples

### TransferRoom (football data marketplace)

```yaml
deep_signals:
  - name: "Sporting director churn"
    source: "League / club official press releases + Transfermarkt management changes"
    hit_criteria: "Sporting director has changed in last 12 months"
    weight: 3
    rationale: >
      New sporting director typically re-evaluates the entire scouting and
      transfer-research stack within 6 months. The window is small.

  - name: "Transfer window approaching"
    source: "Official league calendars"
    hit_criteria: "Within 60 days of a transfer window opening (summer or winter)"
    weight: 2
    rationale: >
      Tools to support transfer decisions are bought before windows, not
      during. The 60-day window is the budget conversation window.

  - name: "FFP / financial pressure"
    source: "League FFP / financial regulation filings (Premier League, Bundesliga, La Liga publish these)"
    hit_criteria: "Club listed in current-season FFP risk band or recently sanctioned"
    weight: 3
    rationale: >
      FFP pressure forces clubs to find cheaper signings or more accurate
      valuations — exactly the pain TransferRoom relieves.
```

### A hypothetical compliance SaaS

```yaml
deep_signals:
  - name: "Open enforcement action"
    source: "Regulator filings (SEC, FCA, etc.)"
    hit_criteria: "Active enforcement action involving prospect in last 18 months"
    weight: 3
    rationale: "Active enforcement = compliance budget unlock"

  - name: "Audit cycle approaching"
    source: "Industry-specific audit calendars; the prospect's own annual report"
    hit_criteria: "Next external audit within 6 months"
    weight: 2

  - name: "Recent compliance officer hire"
    source: "LinkedIn searches for prospect company"
    hit_criteria: "Senior compliance role filled in last 6 months"
    weight: 2
    rationale: "New compliance leaders re-evaluate tooling within their first 90 days"
```

## Common mistakes

**Defining "Founded in last 2 years" as a signal.** This is a *fact*, not a buying-trigger. Founding events happen once. They don't stack with anything else.

**Defining "Headquartered in [growth market]" as a signal.** This is a *firmographic*, not a pain signal. Firmographics belong in your `icp` and `fit_score`, not your Pain Stack.

**Mixing v1 and v2.** "Bad reviews" is v1. "Bad reviews trending down for 90 days against a steady industry benchmark" is v2. The latter requires more work to harvest; that's why it's v2.

**Setting 8+ signals.** More signals dilute the stack. If too many fire on every prospect, the HIT tag stops meaning anything. Cap at 5.

**Picking signals you can't actually harvest.** "Customer churn rate" is great in theory; impossible in practice unless you have private data. Drop it.

## When to revise your Pain Stack

- After your first 30 days of weekly runs, compare HIT-tagged prospects against your pipeline. Are they actually closing more often?
- Quarterly review: drop the lowest-correlation signal, add a candidate from your closed-won analysis.
- Annual review: revisit the entire stack against your won/lost data. The market evolves; the signals should too.
