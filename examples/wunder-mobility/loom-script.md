# Loom script — Wunder Radar walkthrough

Target length: **3 minutes**, hard cap.
Audience: Wunder Mobility hiring team (CEO, VP Sales, Head of CS).
Goal: prove I can think and build, not pitch myself.

---

## Setup before recording

1. Have the GitHub repo open in one tab (or this folder in Finder if not on GitHub yet).
2. Have `output/2026-05-12-top-10.md` open in a markdown previewer (or in the editor with preview).
3. Have `output/briefs/europcar-on-demand.md` ready to scroll to.
4. Have a tab open to Europcar On Demand's App Store page (for the screen-share moment).

---

## Beat-by-beat

### Beat 1 — Opening (0:00 – 0:15)

"Hi Gunnar, Bojan, Minh. I'm Can. Before I tell you about me, I want to show you what I built for you this weekend."

[Cut to repo / folder]

### Beat 2 — What it is (0:15 – 0:45)

"This is Wunder Radar. It's a multi-agent system that watches the global universe of shared mobility operators for switching signals — public evidence that an operator is at a re-platforming inflection point. Every week it produces a ranked top 10 with a why-now hypothesis for each."

[Open output/2026-05-12-top-10.md]

"This is what it produced this week, against a universe of 53 active operators I built from your customer page, your competitors' case studies, and EU mobility press."

### Beat 3 — Top 10 walkthrough (0:45 – 1:30)

"Top of the list this week: Europcar On Demand, scored 80 out of 125. Their app store rating is 2.1 out of 5 and hasn't recovered since the 2024 rebrand. That's a platform problem dressed as a UX problem."

[Quickly scroll to show ranking]

"Number two: Cooltra Group. You already power Spain. Cooltra-Cityscoot-felyx are three legacy stacks 18 months into integration. This is an expansion play, not a cold prospect."

"Number three: Nextbike. Sold to Star Capital PE last May, 115,000 bikes across 300 cities. PE-led platform reviews happen in months 6 to 12 — we're in that window now. Forest is the proof point at city scale."

### Beat 4 — Drill into one (1:30 – 2:15)

[Switch to europcar-on-demand.md]

"Each prospect gets a deep dive. Here's the Europcar brief. It has the buying committee — Director of On Demand as champion, Group CTO and CFO as economic buyers. It has the migration scope: 8 to 10 months, city by city. It has the three-message outreach sequence — I'll read the first one."

[Read the first cold message aloud, 15 seconds]

"This passes a 3 WHYs audit: why them is the 2.1 rating, why you is GreenMobility as proof, why act is a 20-minute conversation with a migration plan."

### Beat 5 — How it works (2:15 – 2:40)

[Quick cut to the agents/ folder]

"Architecture is five Claude subagents — news, jobs, app store reviews, LinkedIn, and a PVP generator. They run in parallel via MCP and tool use. The scoring rubric is in scoring.md and the weights should be tuned against your actual closed-won data in the first 90 days. The Python runner takes any operator name and produces a fresh brief in under 2 minutes."

### Beat 6 — Close (2:40 – 3:00)

"Three things I want you to know. One: I built this because I think it's the kind of work the AI-Native GTM Associate would do in the first month. Two: I'm an AE selling AI products into football clubs at TransferRoom right now — so I'm not theorizing about AI-native enterprise sales, I'm doing it. Three: if I joined, the four operators on the top of that list could be in pipeline in 30 days. Talk soon."

---

## Things to NOT do

- Don't introduce myself before showing the build.
- Don't use the word "leverage."
- Don't apologize for anything ("I know this is rough", "sorry for the length").
- Don't say "AI agents" — say "Claude subagents" or just describe what they do.
- Don't show code. Show output. The code is in the repo if they want to dig.
- Don't ramble. Hard 3-minute cap. Re-record if I go over.

## Things to DO

- Look at the camera at beat 1 and beat 6. Screen-share the middle.
- Speak slightly slower than feels natural. Loom over-speeds when nervous.
- Show actual output, not slides. They've seen 300 cover letter Looms; nobody else will hand them top-10 prospects.
- Mention the AE-at-TransferRoom angle exactly once. Don't sell, just frame.
