# Tuning scoring weights

The default scoring (`Pain × Timing × Fit`, equal weight) is a reasonable starting point. After 1-2 weekly runs you'll see whether it's actually predictive for your business.

## The honest test

Take your last 10 **closed-won** deals. For each, reconstruct the signals that were public *at the time you started pursuing them*. Run them through the scoring rubric. They should land in the **top quartile** of your universe.

If they don't:

- **Most won deals were timing-driven (M&A, exec change, expansion):** raise the Timing weight.
- **Most won deals had broken existing tooling / public pain:** raise the Pain weight.
- **Most won deals were "shape of the product was exactly right":** raise the Fit weight.

## How to set weights

In `config/target.yaml`, add a `scoring` block:

```yaml
scoring:
  weights:
    pain: 1.0
    timing: 1.5
    fit: 1.0
```

The runner reads this and multiplies the raw scores. If `weights` is absent, all default to 1.0.

## When the scoring just doesn't work

If you run the rubric against your closed-won data and they score in the *bottom* quartile, the problem isn't the weights — it's the **anchors**.

Open `scoring/rubric.md`. Look at the Pain / Timing / Fit anchor tables. Compare them to what was actually true about your won deals. The anchors probably describe an idealized customer, not your actual customer.

Rewrite the anchors based on your real wins, not the example template.

## What NOT to tune

- **The multiplicative formula (Pain × Timing × Fit).** If you make it additive, you'll get prospects scoring 5 / 0 / 5 = 25 instead of 0. The whole point is that a zero on any dimension means "not now."
- **The 0-5 score range.** The rubric expects integers. Going to 0-10 or 0-100 just adds noise.
- **The tiebreakers.** ACV-first is right for almost every B2B SaaS context. Geography overlap second is right for any sale with reference dependencies.

## Calibration cadence

- **First 90 days:** weekly review of the top-10 brief against what you actually closed/lost. Adjust anchors aggressively.
- **After 90 days:** monthly review. Adjust weights once per quarter at most.
- **After 1 year:** the rubric should be stable. If you're still tuning monthly, something else is wrong (probably the universe is mis-defined, not the scoring).

## A note on Goodhart

Once your team optimizes against the scoring, the scoring will stop being a good predictor. This is unavoidable.

Mitigation: keep a small set of "wild card" criteria that don't feed the score but show up in the brief — gut signals from the team, hunches, references. Use these as sanity checks against the ranked list.
