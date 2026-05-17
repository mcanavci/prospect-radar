# Deploying to Vercel

Optional. The weekly brief renders fine as markdown in your editor, but a deployed one-pager is useful when you want to:

- Share a public link with hiring managers / investors as portfolio proof
- Give your sales team a single URL to reference each Monday
- Embed the latest brief in your team's Notion / Slack / dashboard

## Setup

```bash
npm install -g vercel    # if you don't have it
vercel login
```

## Convert the latest brief to HTML

The repo ships with a simple converter (or write your own — the brief is plain markdown, render however you want).

```bash
# From repo root
python tools/brief_to_html.py output/2026-05-12-top-10.md > index.html
```

(If `tools/brief_to_html.py` doesn't exist yet, write it — the Wunder example's `one-pager.html` is a working reference for what the output should look like.)

## Deploy

```bash
vercel deploy
```

The first deploy will prompt you to:
1. Link to a Vercel account
2. Pick a project name (e.g. `your-radar`)
3. Confirm the build settings

After that, every `vercel deploy` redeploys the latest `index.html`.

For a custom domain:

```bash
vercel domains add your-radar.com
```

## What gets deployed

By default, only `index.html` and any static assets. The repo's `.vercelignore` (if present) excludes everything else.

**Important:** your `config/target.yaml` and `data/universe.yaml` should NEVER be deployed. They're in `.gitignore` already. Double-check `.vercelignore` if you're paranoid.

## Privacy tradeoff

A public Vercel deployment means:

- **Pro:** Shareable URL. Portfolio proof. Embeddable.
- **Con:** Your competitors can see your top-ranked prospects.

For a *portfolio* deployment (you're showcasing the work, not running live outbound), publish freely.

For a *team-internal* deployment (active outbound), set the Vercel project to password-protected or deploy to a private subdomain.

## The Wunder Radar precedent

[wunder-radar.vercel.app](https://wunder-radar.vercel.app) is the canonical example. It's a public deploy because the use case was portfolio + job application, not live outbound. The data inside (operator names, scoring, draft messages) was all from public sources — no proprietary customer data.

That's the safe pattern for public deploys: if every fact in your brief is sourced from public press / app stores / LinkedIn / job postings, you can publish without leaking anything you didn't already pay for.
