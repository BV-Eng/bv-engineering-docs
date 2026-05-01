# PeopleBot

Executive recruiting pipeline for Better Ventures. Scrapes LinkedIn profiles via PhantomBuster, enriches with Affinity warmth data, and uses Claude AI to score and rank candidates against job descriptions.

## How It Works

1. User pastes a LinkedIn search URL and a job description or hiring criteria into the PeopleBot web app.
2. User selects which investor's LinkedIn account to run the search (Rick, Wes, or Lyndsey) and how many profiles to scrape (5–50).
3. PhantomBuster's LinkedIn Search Export phantom runs the search using the selected investor's LinkedIn account, exporting up to 50 candidate profiles.
4. PhantomBuster's Profile Scraper phantom auto-chains after the search, scraping detailed profile data (summary, education, skills, past companies) using a separate account to protect investor profiles.
5. Affinity enrichment looks up each candidate to find existing BV relationships. Warmth tiers are assigned based on email recency: **Hot** (< 6 months), **Warm** (< 1 year), **Lukewarm** (< 18 months), **Cold** (no recent contact).
6. Claude Haiku 4.5 analyzes the job description and extracts 4–6 role-specific evaluation criteria with weights. Each candidate is then scored 1–10 on every criterion with written reasoning.
7. Results are displayed as a ranked list sorted by overall relevance score (0–100). Users can expand any candidate to see the full score breakdown, shortlist candidates, and export to CSV.

## Tech Stack

| Component  | Detail                                                        |
|------------|---------------------------------------------------------------|
| Language   | TypeScript (Next.js 16 with App Router API routes)            |
| LLM        | Claude Haiku 4.5 — adaptive criteria extraction + scoring     |
| Database   | Supabase PostgreSQL (tables: pb_searches, pb_candidates)      |
| Deployment | Vercel (peoplebot.vercel.app)                                 |
| Scraping   | PhantomBuster — LinkedIn Search Export + Profile Scraper       |

## Investor Accounts

Each search runs under one partner's LinkedIn account. The app enforces a daily limit of **50 profiles per investor per day**.

| Investor | Configuration | LinkedIn Plan |
|----------|--------------|---------------|
| Rick     | Default identity in PhantomBuster dashboard | Sales Navigator Core |
| Wes      | Session cookie + user agent via Vercel env vars | TBD |
| Lyndsey  | Session cookie + user agent via Vercel env vars | TBD |

- **Rick** is configured directly in PhantomBuster as the default identity. No cookie needed in env vars.
- **Wes and Lyndsey** require `LINKEDIN_{NAME}_COOKIE` and `LINKEDIN_{NAME}_USER_AGENT` env vars in Vercel. These are passed to PhantomBuster via `bonusArgument` at launch time.
- Cookies expire periodically. When they do, the investor needs to send a fresh `li_at` cookie and user agent from the same browser session.

## AI Scoring

Scoring is **adaptive** — criteria are extracted from the job description, not hardcoded.

1. **Criteria extraction** (1 Claude call): Analyzes the JD and produces 4–6 weighted evaluation criteria specific to the role.
2. **Candidate scoring** (1 Claude call per candidate): Scores each candidate 1–10 on every criterion with reasoning. Overall score is the weighted average mapped to 0–100.
3. **Rescore**: Users can supplement or override the original JD and rescore all candidates with updated criteria.

Scoring runs in batches of 10 to stay within Vercel's 60-second function timeout.

## Affinity Integration

- **Lists:** Not list-specific; reads relationship/email interaction data across BV's Affinity account.
- **Reads:** Last email contact dates between BV team members and candidate contacts for warmth scoring.
- **Writes:** Nothing. Read-only integration.

## Data Sources

- LinkedIn (people search URLs, pasted by user)
- PhantomBuster (LinkedIn Search Export + Profile Scraper phantoms)
- Affinity (email interaction history for warmth scoring)
- Claude Haiku 4.5 (candidate evaluation against job descriptions)

## How to Access / Use

**Live at [peoplebot.vercel.app](https://peoplebot.vercel.app)**

1. Go to PeopleBot → New Search
2. Enter a search title and paste a LinkedIn search URL
3. Paste or describe the job description / hiring criteria
4. Select investor and number of profiles
5. Click Create Search — the full pipeline runs automatically (~5–10 min)
6. Review ranked results, expand score breakdowns, shortlist, and export CSV

**Methodology page:** [peoplebot.vercel.app/methodology](https://peoplebot.vercel.app/methodology)

## Key Configuration

| Variable                        | Location       | Purpose                                     |
|---------------------------------|----------------|---------------------------------------------|
| DATABASE_URL                    | Vercel Env     | Supabase PostgreSQL pooler connection       |
| PHANTOMBUSTER_API_KEY           | Vercel Env     | PhantomBuster API access                    |
| PHANTOMBUSTER_SEARCH_PHANTOM_ID | Vercel Env     | Search Export phantom (2104329820276624)     |
| PHANTOMBUSTER_SCRAPER_PHANTOM_ID| Vercel Env     | Profile Scraper phantom (4551914483010813)   |
| ANTHROPIC_API_KEY               | Vercel Env     | Claude AI for scoring                       |
| AFFINITY_API_KEY                | Vercel Env     | Affinity warmth data                        |
| LINKEDIN_WES_COOKIE             | Vercel Env     | Wes's LinkedIn session cookie               |
| LINKEDIN_WES_USER_AGENT         | Vercel Env     | Wes's browser user agent                    |
| LINKEDIN_LYNDSEY_COOKIE         | Vercel Env     | Lyndsey's LinkedIn session cookie           |
| LINKEDIN_LYNDSEY_USER_AGENT     | Vercel Env     | Lyndsey's browser user agent                |

## Known Limitations

- No authentication — anyone with the URL can access the app.
- LinkedIn cookies expire periodically and must be manually refreshed.
- PhantomBuster may miss private LinkedIn profiles or return limited data for sparse profiles.
- Warmth scoring only reflects email contact via Affinity; meetings, calls, and other interactions are not captured.
- Scoring quality depends on LinkedIn profile completeness — candidates with sparse profiles get lower-confidence scores.
- Daily limit of 50 profiles per investor is enforced in-app but not at the PhantomBuster level.
