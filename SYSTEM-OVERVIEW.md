# System Overview

Full architecture narrative for Better Ventures' engineering stack.

---

## The Three Pillars

### 1. Deal Flow (Sourcing -> Scoring -> Evaluation)

Three sourcing bots continuously feed qualified companies into Affinity CRM. Each bot targets a different sourcing channel but shares the same scoring rubrics and thesis alignment logic.

#### AcceleratorBot

Scrapes 30+ accelerator and incubator batch pages weekly using crawl4ai and Claude Haiku 4.5. Each discovered company is scored 1-10 against BV's investment thesis across three themes (Climate/Sustainability, Health, Workforce). Companies scoring 6+ and founded after 2020 are pushed to Affinity list 324289. A post-push cleanup step removes companies with >$10M in funding to keep the pipeline focused on early-stage opportunities.

- **Schedule**: GitHub Actions, every Wednesday
- **LLM**: Claude Haiku 4.5
- **Affinity List**: 324289

#### DealBot

Processes PitchBook CSV/XLSX exports, scoring companies on 3 themes (Climate, Health, Workforce) using Claude Haiku 4.5, and filters at a threshold of 5+. Also scores founders from PhantomBuster LinkedIn scrapes to assess team quality. Qualified companies and founders are pushed to Affinity list 169980.

- **Schedule**: GitHub Actions, triggered by CSV upload to the repo's `input/` folder
- **LLM**: Claude Haiku 4.5
- **Affinity List**: 169980

#### StealthBot

Processes LinkedIn outreach contacts sourced via Google Sheets and PhantomBuster. Scores founders using Claude Sonnet 4.6, determines thesis fit, and auto-assigns a BV team owner based on theme alignment. Pushes to Affinity list 335121 (persons list).

- **Schedule**: GitHub Actions, 4x/week
- **LLM**: Claude Sonnet 4.6
- **Affinity List**: 335121

#### Shared Across All Bots

- **Scoring Rubrics**: All three bots reference the same centralized scoring criteria from the bv-rubrics repo, fetched at runtime via GitHub raw URLs. This ensures consistent thesis alignment regardless of sourcing channel.
- **Auto-Owner Assignment**: All three bots auto-assign deal owners based on theme: Rick (Climate/Sustainability), Wes (Health/Food), Lyndsey (Workforce/Education).

#### BV Pipeline Dashboard

BV Pipeline sits on top of Affinity as the team's primary deal evaluation interface.

- Syncs all 3 Affinity lists into a Supabase PostgreSQL database
- Provides a 4-stage AI evaluation workflow: **Quick Screen -> Deck Eval -> Post-Call -> Validation (Deep Diligence)**
- Uses Claude Sonnet 4.6 with web search for research at each evaluation stage
- Writes evaluation links and pipeline status back to Affinity, keeping CRM and dashboard in sync
- Live at [bv-pipeline.vercel.app](https://bv-pipeline.vercel.app)

---

### 2. Portfolio Tools (Supporting Portfolio Companies)

#### Raisebot

Investor database containing 2000+ investors. Data was imported from Affinity CSV exports and PitchBook. AI matching via Claude Haiku 4.5 extracts company profiles from portfolio company descriptions and scores investor relevance. Displays BV team connection strength derived from Affinity relationship data, so portfolio founders can leverage warm intros.

- **Live**: [raisebot-fawn.vercel.app](https://raisebot-fawn.vercel.app)
- **LLM**: Claude Haiku 4.5
- **Database**: Supabase (investors + people tables)

#### PeopleBot

Executive recruiting pipeline for portfolio companies. Generates LinkedIn Sales Navigator search URLs based on role requirements, triggers PhantomBuster scraping of candidate profiles, and enriches results with Affinity relationship recency data ("warmth" scoring) to surface candidates with existing BV network connections.

- **Status**: Not yet deployed to Vercel
- **Data Sources**: LinkedIn (via PhantomBuster), Affinity

---

### 3. Information (Keeping the Team Informed)

#### NewsBot

Personalized RSS and news aggregation dashboard. Each team member (Rick, Wes, Lyndsey) has customized feed folders aligned to their investment themes. Tracks 71 portfolio companies via Google News feeds. Ingests relevant industry newsletters via Zapier. Includes Slack sharing integration for distributing key articles to the team.

- **Live**: [news-bot-4nee.vercel.app](https://news-bot-4nee.vercel.app)
- **Storage**: Upstash Redis
- **Integrations**: Zapier (newsletter ingestion), Slack (article sharing)

---

## Shared Infrastructure

| Service | Role |
|---------|------|
| **Affinity CRM** | Central system of record. All deal bots write to it, BV Pipeline reads from it and writes back. |
| **GitHub Actions** | All bots run as scheduled or triggered GitHub Actions workflows. |
| **Vercel** | Hosts BV Pipeline, NewsBot, and Raisebot. Auto-deploys from main branch. |
| **Supabase** | PostgreSQL for BV Pipeline (companies + evals tables) and Raisebot (investors + people tables). |
| **Upstash Redis** | NewsBot feed and newsletter storage. |
| **PhantomBuster** | LinkedIn scraping for DealBot (founder profiles), StealthBot (outreach contacts), and PeopleBot (candidate search). |
| **bv-rubrics** | Single source of truth for scoring criteria, consumed by all bots at runtime via GitHub raw URLs. |

### LLMs Used

| Tool | Model |
|------|-------|
| AcceleratorBot | Claude Haiku 4.5 |
| DealBot | Claude Haiku 4.5 |
| StealthBot | Claude Sonnet 4.6 |
| BV Pipeline | Claude Sonnet 4.6 |
| Raisebot | Claude Haiku 4.5 |

---

## Data Flow Summary

The end-to-end flow follows a clear path from external sources to investment decisions:

1. **External Sources** (accelerator websites, PitchBook exports, LinkedIn outreach contacts) are ingested by the three sourcing bots.
2. **Bots** score and filter companies/founders against BV's thesis using shared rubrics, then push qualified entries into Affinity CRM with owner assignments and scores.
3. **Affinity** serves as the central system of record. All deal data converges here.
4. **BV Pipeline** syncs from Affinity into Supabase, providing the team with a structured 4-stage evaluation workflow powered by AI research.
5. **Investment Decisions** are informed by the cumulative scoring, research, and evaluation data surfaced through BV Pipeline.

This creates a funnel: broad automated sourcing at the top, progressively deeper AI-assisted evaluation in the middle, and human decision-making at the bottom -- supported at every stage by consistent data and scoring.

---

## Diagrams

Visual architecture and data flow diagrams are available in the [`diagrams/`](diagrams/) folder. These include Mermaid source files that can be rendered in any Mermaid-compatible viewer (GitHub, VS Code, etc.).
