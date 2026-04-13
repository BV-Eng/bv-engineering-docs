# BV Tools — Quick Guide for Venture Partners

This guide is for the BV team (Rick, Wes, Lyndsey, Katie). It covers every tool we have, what it does, and how to use it. No technical knowledge required.

---

## Your Daily Tools

These are the tools you interact with directly in your browser.

---

### 1. BV Pipeline (bv-pipeline.vercel.app/pipeline)

**What it is:** Your deal evaluation dashboard. This is where you see all sourced companies, run AI-powered evaluations, and track where each deal stands in the pipeline.

**How to use it:**

- **See your deals:** Open the dashboard and you will see a table of all companies. You can sort and filter by status, owner, score, or source.
- **Pull in new data:** Click the "Sync from Affinity" button to bring in the latest companies and updates from Affinity. This may take a few minutes.
- **Evaluate a company:** Click on any company to see its details, then use the tabs across the top to move through evaluation stages:
  - **Quick Screen** — Paste a company URL or description. The AI researches the company and scores it across 9 criteria.
  - **Deck Eval** — Upload a pitch deck (PDF). The AI reads the deck and scores it across 22 criteria.
  - **Post-Call** — After you talk to a founder, paste your call notes here. The AI updates scores based on what you learned.
  - **Validation** — Run a deep diligence analysis. This takes longer (up to 5 minutes) and produces a detailed memo.
- **Update deal status:** You can change the pipeline stage and deal owner directly in the table. These changes sync back to Affinity automatically.

**Pipeline stages:** Quick Screen, Deck Eval, Post-Call, Validation

---

### 2. NewsBot (news-bot-4nee.vercel.app)

**What it is:** Your personalized news dashboard. Each team member has their own curated set of news feeds.

**How to use it:**

- **Pick your profile:** On the home screen, select your name.
- **Browse your feeds:** Use the sidebar to switch between different feed categories.
- **Filter by time:** Use the time buttons (1d, 7d, 30d, etc.) to control how far back to show articles.
- **Share to Slack:** Click the share button on any article to post it to a Slack channel.
- **Save for later:** Bookmark articles you want to come back to.

**Good to know:**

- Wes's feeds are organized by investment theme keywords (climate, health, workforce, etc.).
- Portfolio company news is in the "PortCos" tab. It tracks 71 companies in our portfolio.
- Newsletters from StrictlyVC, Pitchbook, and others automatically show up here via Zapier.

---

### 3. Raisebot (raisebot-fawn.vercel.app)

**What it is:** An investor database for our portfolio companies. When a portfolio company needs to fundraise, this tool finds investors that match their profile.

**How to use it:**

- **AI Matching:** Type a description of the company into the AI Matching box and hit search. The AI finds the best-fit investors.
- **Manual filters:** Use filters for stage, sector, location, and check size to narrow results.
- **Review results:** Each result shows a match score and BV connection strength — meaning which BV team member knows the investor and how strong the relationship is.
- **Save and export:** Save investors to shortlists. Export results to CSV for sharing.

---

## Things That Run Automatically

These tools run on their own. You do not need to do anything — they just work in the background.

- **AcceleratorBot** — Scrapes 30+ accelerator websites every Wednesday. Companies that score well automatically appear in Affinity's AcceleratorBot list. They show up in the BV Pipeline dashboard the next time you sync.

- **DealBot** — When PitchBook exports are uploaded to its GitHub repository, it automatically scores the companies and pushes high-scoring ones to Affinity's DealBot list.

- **StealthBot** — Runs 4 times per week. It scores founders from LinkedIn outreach and updates Affinity with scores, thesis fit tags, and owner assignments.

- **Scoring Rubrics** — All bots use the same shared scoring criteria. These are defined in simple text files (YAML format) in the bv-rubrics repository on GitHub. If you want to change how companies or founders are scored, these files can be edited directly on GitHub.

---

## Not Yet Live

- **PeopleBot** — An executive recruiting tool that searches LinkedIn and enriches candidates with Affinity relationship data. It has been built but is not deployed yet.

---

## Need Something Changed?

| What you want | How to do it |
|---------------|-------------|
| Update scoring criteria | Edit the YAML files at github.com/BV-Eng/bv-rubrics |
| Add a new accelerator to track | Edit `accelerators.json` in the acceleratorbot repo |
| Add RSS feeds to NewsBot | Click the "+" button in the NewsBot sidebar |
| Anything else | Open Claude Code in the relevant repo directory and describe what you need. Use git identity `eng@better.vc` for all commits. |
