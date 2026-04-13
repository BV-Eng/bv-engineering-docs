# Better Ventures Engineering Tools Guide

Last updated: April 13, 2026

This document covers all engineering tools built for Better Ventures, how they work, and how to use them.

---

## At a Glance

Better Ventures has three categories of engineering tools:

1. **Deal Flow** -- Automated sourcing, scoring, and evaluation of potential investments
2. **Portfolio Tools** -- Support for portfolio companies (investor matching, recruiting)
3. **Information** -- News aggregation and team intelligence

All code lives under https://github.com/BV-Eng

---

## DEAL FLOW TOOLS

### How Deal Flow Works

Three automated bots find and score potential deals from different sources. Each bot runs on a schedule, scores companies or founders against BV's investment thesis, and pushes qualifying entries into Affinity CRM. A dashboard (BV Pipeline) sits on top of Affinity to provide deeper AI-powered evaluation.

The scoring criteria are shared across all bots via a central configuration file (bv-rubrics) so that scoring is consistent.

Deal owners are auto-assigned based on thesis fit:
- Rick Moss -- Climate, Sustainability, Electrification
- Wes Selke -- Health, Food, Nutrition, Bioeconomy
- Lyndsey Boucherle -- Workforce, Education, Circular Economy

---

### AcceleratorBot

What it does: Automatically scrapes 30+ accelerator and incubator batch pages (YC, IndieBio, MIT Delta V, Plug and Play, and more). Uses AI to extract company information, score each company against BV's investment thesis, and push high-scoring deals into Affinity.

How it runs: Automatically every Wednesday morning. No action needed.

What happens:
1. Crawls accelerator websites for new batch/portfolio pages
2. Extracts company names, descriptions, URLs, and founder info
3. AI scores each company 1-10 on thesis alignment
4. Companies scoring 6 or higher are pushed to the AcceleratorBot list in Affinity
5. After pushing, removes companies that were founded before 2020 or have raised more than $10M

GitHub: https://github.com/BV-Eng/acceleratorbot

To add a new accelerator: Edit the accelerators.json file in the repository and commit. The bot will include it on the next weekly run.

---

### DealBot

What it does: Takes company data exported from PitchBook, scores each company on three investment themes (Climate/Sustainability, Health/Longevity, Workforce/Opportunity), and pushes qualifying companies to Affinity. Also scores founders using LinkedIn data.

How it runs: Triggered automatically when a PitchBook CSV or Excel file is uploaded to the repository's input/ folder.

What happens:
1. Reads the PitchBook export file
2. AI scores each company on three themes (1-10 each)
3. Companies scoring 5 or higher on any theme pass the filter
4. Qualifying companies are pushed to the DealBot list in Affinity
5. Owners are auto-assigned based on which theme scored highest

GitHub: https://github.com/BV-Eng/dealbot

---

### StealthBot

What it does: Processes LinkedIn outreach contacts. When the team reaches out to founders via LinkedIn campaigns, StealthBot creates those contacts in Affinity and later scores them using AI to determine thesis fit.

How it runs: Two automated workflows:
- Sync: Runs instantly when a new contact is added to the outreach Google Sheet
- Enrichment: Runs 4 times per week (Mon, Wed, Thu, Fri at 10am Pacific)

What happens:
1. New outreach contacts are synced from Google Sheets to Affinity
2. LinkedIn profile data is pulled from PhantomBuster
3. AI scores each founder 1-10 and determines thesis fit (Climate, Health, or Workforce)
4. Score, reasoning, thesis fit, and owner are written to Affinity

GitHub: https://github.com/BV-Eng/stealthbot

---

### Scoring Rubrics (Shared Configuration)

All three bots use the same scoring criteria, stored as simple configuration files on GitHub. This ensures consistent scoring across all deal sources.

The rubrics define:
- Company scoring: Three investment themes with keywords and scoring guidance
- Founder scoring: Holistic evaluation criteria
- Investor routing: Which team member owns which theme
- Prestigious investors: List of top VCs/accelerators for flagging notable deals

To change scoring criteria: Edit the YAML files at https://github.com/BV-Eng/bv-rubrics and commit. All bots automatically pick up changes on their next run.

---

### BV Pipeline (Deal Evaluation Dashboard)

What it does: A web-based dashboard that sits on top of Affinity. Shows all sourced deals in one place and provides 4 stages of AI-powered evaluation for deeper analysis.

URL: https://bv-pipeline.vercel.app/pipeline

The 4 evaluation stages:
1. **Quick Screen** -- Enter a company name. AI does web research and scores 9 criteria (problem severity, market size, competition, founder fit, etc.). Takes 1-2 minutes.
2. **Deck Eval** -- Upload a pitch deck PDF. AI analyzes it alongside web research and scores 22+ criteria. Generates call prep questions.
3. **Post-Call** -- Paste call notes. AI updates scores, generates personality assessment, and produces conviction narrative.
4. **Validation** -- One-click deep diligence. AI runs 5 research steps (market/policy, competitive landscape, team signals, comparable exits, synthesis). Generates IC-ready memo.

Key features:
- Pipeline tab shows all companies from all three Affinity lists
- Change pipeline status or owner directly in the table (syncs back to Affinity)
- Click any company to see Affinity data, contact info, and financing context
- Export evaluations as HTML or JSON
- Evaluation links are automatically written back to Affinity

---

## PORTFOLIO TOOLS

### Raisebot (Investor Database)

What it does: Helps BV portfolio companies find the right investors for fundraising. Contains 2000+ investors with detailed profiles, and uses AI to match investors to a company's specific profile.

URL: https://raisebot-fawn.vercel.app

How to use:
1. **AI Matching**: Describe the portfolio company in the text box (e.g., "Series A synthetic biology startup developing novel enzymes for industrial decarbonization"). AI extracts a structured profile and ranks all investors by relevance.
2. **Filter Search**: Browse by stage, sector, location, check size, investor type, and BV connection strength.
3. **Connection Data**: Each investor card shows which BV team member has a relationship and how strong it is (from Affinity).
4. **Shortlists**: Save promising investors to named lists and export to CSV for outreach.

GitHub: https://github.com/BV-Eng/raisebot

---

### PeopleBot (Executive Recruiting)

What it does: Helps find executive candidates by searching LinkedIn via Sales Navigator, scraping profiles, and scoring candidates by "warmth" -- how recently someone at BV has been in email contact with them.

Status: Built but NOT YET DEPLOYED. Code is ready at https://github.com/BV-Eng/peoplebot

How it would work:
1. Enter search criteria (job title, location, keywords, target companies)
2. System generates a LinkedIn Sales Navigator search
3. PhantomBuster scrapes matching profiles
4. Each candidate is enriched with Affinity email history
5. Candidates are ranked by warmth tier (hot, warm, lukewarm, cold)

---

## INFORMATION TOOLS

### NewsBot (News Dashboard)

What it does: Personalized news aggregation dashboard. Each team member has their own curated set of RSS feeds organized by investment theme.

URL: https://news-bot-4nee.vercel.app

How to use:
1. Select your name on the home screen (Rick, Wes, or Lyndsey)
2. Browse your personalized feeds in the sidebar
3. Click "PortCos" to see news about 71 portfolio companies
4. Click "Newsletters" to see content from StrictlyVC, Pitchbook, etc.
5. Use time filters (1 day, 7 days, etc.) to control how far back to look
6. Share articles to Slack or save them for later

Wes's feeds are heavily organized by keyword monitors across investment themes.

To add a new feed: Click the "+" button in the sidebar and enter an RSS feed URL or Google News search URL.

GitHub: https://github.com/BV-Eng/newsbot

---

## IMPORTANT INFORMATION FOR MAINTENANCE

### Git Identity
All code changes must use the email eng@better.vc. Vercel deployments are configured to only accept this identity.

### How Deployments Work
- Web apps (BV Pipeline, NewsBot, Raisebot) are hosted on Vercel and auto-deploy when code is pushed to the main branch
- Bots (AcceleratorBot, DealBot, StealthBot) run via GitHub Actions on their own schedules
- No manual deployment steps are needed -- just push code

### API Keys and Secrets
API keys are stored in two places:
- **GitHub Secrets** (per repository) -- for bots that run via GitHub Actions
- **Vercel Environment Variables** -- for web apps

Services with API keys: Anthropic (Claude AI), Affinity CRM, Hunter.io, PhantomBuster, Supabase, Upstash Redis

### If Something Breaks
For any of these tools, you can open Claude Code in the relevant repository directory and describe the issue. The CLAUDE.md files in each repo contain context about the codebase. Remember to use eng@better.vc for any commits.

### GitHub Repositories Quick Reference

| Tool | Repository | Type |
|------|-----------|------|
| AcceleratorBot | github.com/BV-Eng/acceleratorbot | Bot (GitHub Actions) |
| DealBot | github.com/BV-Eng/dealbot | Bot (GitHub Actions) |
| StealthBot | github.com/BV-Eng/stealthbot | Bot (GitHub Actions) |
| Scoring Rubrics | github.com/BV-Eng/bv-rubrics | Config (YAML) |
| BV Pipeline | github.com/BV-Eng/bv-pipeline | Web App (Vercel) |
| NewsBot | github.com/BV-Eng/newsbot | Web App (Vercel) |
| Raisebot | github.com/BV-Eng/raisebot | Web App (Vercel) |
| PeopleBot | github.com/BV-Eng/peoplebot | Not Deployed |

### Live URLs

| Tool | URL |
|------|-----|
| BV Pipeline | https://bv-pipeline.vercel.app/pipeline |
| NewsBot | https://news-bot-4nee.vercel.app |
| Raisebot | https://raisebot-fawn.vercel.app |

---

## SUBSCRIPTIONS & BILLING

### Monthly Costs (~$328/month total)

| Service | Cost/Month | What It Powers | Account |
|---------|-----------|----------------|---------|
| Anthropic (Claude Code CLI) | $100 | Development and maintenance tool | eng@better.vc |
| Anthropic (API credits) | Variable (~$20-50) | AcceleratorBot, DealBot, StealthBot, BV Pipeline, Raisebot | eng@better.vc |
| Vercel | $20 | Hosts BV Pipeline, NewsBot, Raisebot | eng@better.vc |
| PhantomBuster | $159 | LinkedIn scraping for DealBot, StealthBot, PeopleBot | eng@better.vc |
| Hunter.io | $49 | AcceleratorBot email enrichment | Rick's account |
| Supabase | Free | Databases for BV Pipeline and Raisebot | eng@better.vc |
| Upstash Redis | Free | NewsBot feed storage | eng@better.vc |
| GitHub | Free | All code repositories and bot automation | BV-Eng org |

### What Each Service Does

- **Anthropic** -- The AI behind all of our tools. Claude scores companies, evaluates founders, researches deals, and matches investors. The $100/month is for the Claude Code development tool. API usage (for the bots and dashboards) is billed separately from prepaid credits. All tools now use Anthropic -- no other AI providers.
- **Vercel** -- Web hosting platform. All three web apps auto-deploy when code is pushed to GitHub. No manual deployment needed.
- **PhantomBuster** -- LinkedIn automation. Scrapes founder/candidate profiles that our bots then score and enrich. This is the second-largest expense.
- **Hunter.io** -- Finds email addresses for founders discovered by AcceleratorBot. Billed under Rick's account. May switch to RocketReach.
- **Supabase** -- Free PostgreSQL databases. BV Pipeline stores company data and evaluation results. Raisebot stores the investor database.
- **Upstash Redis** -- Free serverless cache. NewsBot stores feed configurations and newsletter articles.
- **GitHub** -- Where all the code lives. Also runs the bot automation (GitHub Actions) on schedules.

### If a Subscription Lapses

| Service | Impact if cancelled |
|---------|-------------------|
| Anthropic API | ALL AI-powered tools stop: AcceleratorBot, DealBot, StealthBot, BV Pipeline evals, Raisebot matching |
| Vercel | BV Pipeline, NewsBot, and Raisebot websites go offline |
| PhantomBuster | LinkedIn scraping stops -- DealBot founder scoring, StealthBot enrichment, and PeopleBot all affected |
| Hunter.io | AcceleratorBot still runs but won't find founder email addresses |
| Supabase | BV Pipeline and Raisebot lose their databases -- data loss risk |

### Action Items

1. **Set up Anthropic auto-billing with spend limits** -- Currently running on prepaid credits. Set a $200/month limit at console.anthropic.com to prevent unexpected charges while ensuring tools don't stop working.
2. ~~**Migrate DealBot from OpenAI to Anthropic**~~ -- DONE. DealBot now uses Claude Haiku 4.5. OpenAI subscription can be cancelled. **Important:** Set the `ANTHROPIC_API_KEY` GitHub secret on the dealbot repo with the real key value.
3. **Evaluate Hunter.io vs RocketReach** -- Consider switching email enrichment provider for better coverage or pricing.
