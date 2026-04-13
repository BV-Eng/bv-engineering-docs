# PeopleBot

Executive recruiting tool that generates LinkedIn Sales Navigator searches, scrapes profiles, and scores candidates by BV relationship warmth.

## How It Works

1. User enters recruiting criteria: target title, location, keywords, and companies.
2. PeopleBot generates a LinkedIn Sales Navigator search URL from the criteria.
3. PhantomBuster scrapes LinkedIn profiles matching the search.
4. Scraped profiles are stored in the PostgreSQL database.
5. Affinity enrichment adds "warmth" scoring based on how recently BV team members have been in email contact with each candidate.
6. Warmth tiers are assigned: **Hot** (< 180 days), **Warm** (< 365 days), **Lukewarm** (< 540 days), **Cold** (540+ days or no contact).
7. Results are displayed in the frontend with warmth indicators and profile details.

## Tech Stack

| Component  | Detail                                    |
|------------|-------------------------------------------|
| Language   | Python 3.11 (FastAPI) + TypeScript (Next.js 14) |
| LLM        | None                                      |
| Database   | PostgreSQL (designed for Vercel Postgres)  |
| Deployment | NOT YET DEPLOYED -- needs Vercel setup    |

## Affinity Integration

- **Lists:** Not list-specific; reads relationship/email interaction data across BV's Affinity account.
- **Reads:** Last email contact dates between BV team members and candidate contacts for warmth scoring.
- **Writes:** Nothing. Read-only integration.

## Data Sources

- LinkedIn Sales Navigator (search URL generation)
- PhantomBuster (LinkedIn profile scraping)
- Affinity (email interaction history for warmth scoring)

## How to Access / Use

**Not yet deployed.** Requires Vercel project setup for both the FastAPI backend and Next.js frontend.

## How to Trigger Manually

1. Go to the repository on GitHub.
2. Click the **Actions** tab.
3. Select the relevant workflow from the left sidebar.
4. Click **Run workflow** (workflow_dispatch).

## Key Configuration

| Variable                         | Location         | Purpose                              |
|----------------------------------|------------------|--------------------------------------|
| DATABASE_URL                     | Env (TBD)        | PostgreSQL connection                |
| PHANTOMBUSTER_API_KEY            | Env (TBD)        | LinkedIn scraping                    |
| PHANTOMBUSTER_SEARCH_PHANTOM_ID  | Env (TBD)        | Specific PhantomBuster phantom ID    |
| AFFINITY_API_KEY                 | Env (TBD)        | Email interaction warmth data        |

## Known Limitations

- Not yet deployed; requires Vercel project creation and environment variable configuration.
- No authentication.
- No LLM; candidate evaluation is purely based on warmth scoring and profile data.
- PhantomBuster scraping is rate-limited and may miss private LinkedIn profiles.
- Warmth scoring only reflects email contact; meetings, calls, and other interactions are not captured.
