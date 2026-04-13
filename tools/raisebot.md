# Raisebot

Investor database with AI-powered search and matching to help portfolio companies find the right investors.

## How It Works

1. User enters a free-text description of their company (stage, sector, what they are building).
2. Claude 3 Haiku extracts a structured company profile from the description (sector, stage, geography, themes).
3. A hybrid 7-layer scoring algorithm ranks all investors in the database by relevance to the company profile.
4. BV team connection strength is pulled from Affinity relationship data and surfaced alongside each investor.
5. Results are displayed as a ranked list with scores, investor details, and BV relationship context.
6. Users can create shortlists and export them as CSV.

## Tech Stack

| Component  | Detail                                    |
|------------|-------------------------------------------|
| Language   | TypeScript (Next.js 14)                   |
| LLM        | Claude Haiku 4.5 (Anthropic)              |
| Database   | Supabase PostgreSQL                       |
| Deployment | Vercel at raisebot-fawn.vercel.app        |

## Affinity Integration

- **Lists:** Not list-specific; uses relationship-level data across BV's Affinity account.
- **Reads:** BV team relationship strength and interaction history with investors.
- **Writes:** Nothing. Read-only integration.
- **Data import:** Investor data is imported offline via Affinity CSV exports, PitchBook Excel files, and enrichment scripts located in the `backend/` directory.

## Data Sources

- Affinity CSV exports (BV team relationship data)
- PitchBook Excel exports (investor profiles and fund data)
- Enrichment scripts in `backend/` (offline processing)

## How to Access / Use

Web application at [https://raisebot-fawn.vercel.app](https://raisebot-fawn.vercel.app).

## How to Trigger Manually

1. Go to the repository on GitHub.
2. Click the **Actions** tab.
3. Select the data import/enrichment workflow from the left sidebar (if applicable).
4. Click **Run workflow** (workflow_dispatch) to re-import investor data.

## Key Configuration

| Variable        | Location     | Purpose                     |
|-----------------|--------------|------------------------------|
| DATABASE_URL    | Vercel Env   | Supabase PostgreSQL connection |
| ANTHROPIC_API_KEY | Vercel Env | Claude 3 Haiku API access    |

- Investor data refresh requires running the `backend/` enrichment scripts manually and re-importing.

## Known Limitations

- No authentication; anyone with the URL can access the tool.
- Investor data is imported offline and can become stale between refreshes.
- The 7-layer scoring algorithm may not capture niche investor preferences well.
- Claude 3 Haiku profile extraction can misinterpret ambiguous company descriptions.
- No write-back to Affinity; shortlists exist only in the app and CSV exports.
