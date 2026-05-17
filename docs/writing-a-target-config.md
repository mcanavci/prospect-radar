# Writing a target config

`config/target.yaml` is the single most important file in your radar. It defines your company, your offer, your customers, and your buyer. Everything else parameterizes off this.

The mistake first-time users make: **filling it out at a marketing-page level of generality.** "Enterprise SaaS for forward-thinking teams" produces unusable briefs. Be specific or skip this whole project.

## The 6 sections, ranked by impact

### 1. `proof_points` (highest impact)

The PVP generator literally cannot produce a usable message without good proof points. This is where 50% of your brief quality lives.

Each entry needs:

```yaml
- customer: "Forest"
  profile: "14K e-bikes, London, fastest-growing UK operator"
  use_for: "Large e-bike share prospects, especially capital-city scale"
  public_evidence: "https://forest.com / TechCrunch coverage 2024-2025"
```

- **`customer`** — real customer name. Has to be a customer the prospect could plausibly look up.
- **`profile`** — specific scale + outcome. "14K e-bikes, London" beats "European market leader."
- **`use_for`** — when the PVP agent should match this to a prospect. Be tight: "Large e-bike share prospects" beats "any e-bike company."
- **`public_evidence`** — where the prospect can verify this is real. Vital. If you don't have public evidence, this proof point won't survive the prospect's 30-second Google check.

Have at least 3 proof points. Ideally 5-8 covering different prospect profiles.

### 2. `icp` (second-highest impact)

`icp` filters the signal harvesters. They use it to decide "is this signal relevant for *this* sale?"

```yaml
icp:
  segment: "Shared-mobility operators with 500+ vehicles..."
  geography: ["Europe (DACH, Nordics...)"]
  exclusions:
    - "Operators under 400 vehicles..."
    - "Vertically integrated giants (Lime, Voi, Bolt)..."
```

The `exclusions` block is doing more work than people realize. It tells the harvesters which signals to *ignore* (e.g., "Lime just raised $500M" is news but not a buying signal for you because Lime won't buy).

### 3. `buyers` (third-highest impact)

The PVP generator uses this to write to the right level.

```yaml
buyers:
  - role: "CTO / VP Engineering"
    typical_pain: "Build-vs-buy debate; engineering team stretched thin"
    typical_proof_that_lands: "Forest cut their internal platform engineering headcount to zero"
```

You probably have 3-5 buyer personas across your accounts. Define each. The `typical_proof_that_lands` field is what the PVP agent reaches for when matching proof points.

### 4. `pricing`

Used to estimate ACV per prospect. Even rough numbers help — the brief reads better when it says "estimated €400-700k ARR based on your published Scale tier" than "ACV TBD."

If your pricing isn't published, you can still set it here (it's gitignored in `.gitignore`). The agents need *something* to anchor on.

### 5. `products`

If you sell multiple products, list them separately. The PVP generator can then articulate value at the product level instead of the company level.

### 6. `sales_motion`

Shapes CTA style. If your motion is "20-min discovery call," the messages will end with a 20-min ask. If it's "send me your last RFP," they'll end with that.

## The 3 WHYs audit, applied to your config

Before you run the first brief, audit your target.yaml the same way the PVP generator audits messages:

**Why them — Observation → Impact**

Read your `icp` aloud. Could you replace it with your competitor's ICP without changing meaning? If yes, tighten until you can't.

**Why you — Proof, not adjectives**

Read your `proof_points`. Does each one name a real customer the prospect could verify in 30 seconds? If you wrote "leading e-bike operator" instead of "Forest, 14K bikes in London" — that's adjective, not proof. Fix it.

**Why act — Time, money, or revenue impact**

Read your `buyers > typical_proof_that_lands`. Each one should articulate a save / make / gain. "Helps with X" is not a proof; "Cut engineering headcount need to zero" is.

## How often to update target.yaml

- **Monthly:** review pricing tiers (are deal sizes drifting?)
- **Quarterly:** review proof points (do you have new flagship customers? remove ones that churned)
- **Whenever you close a deal:** add the won deal's pre-sale signals as a calibration data point in `scoring/rubric.md`

## Don't put this in target.yaml

- **Buyer names and emails** — `target.yaml` is for the OFFER. The buyer database lives elsewhere (your CRM).
- **Pricing you don't actually charge** — the PVP agent's ACV estimates depend on these numbers being real. Lying here means lying in your briefs.
- **Proof points that aren't really customers** — pilots, paid POCs, free tier users. If they're not paying production, they're not proof.
