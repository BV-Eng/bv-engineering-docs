# BV Pipeline

4-stage AI deal evaluation dashboard that syncs with Affinity and guides companies through the BV investment process.

## How It Works

1. **Stage 1 - Quick Screen:** Claude performs web research on a company and scores it across 9 criteria. Produces a summary and go/no-go recommendation.
2. **Stage 2 - Deck Eval:** User uploads a pitch deck PDF. Claude analyzes it against 22+ criteria covering market, team, traction, and financials.
3. **Stage 3 - Post-Call:** User pastes call notes. Claude generates updated scores, identifies key risks, and performs personality typing on founders.
4. **Stage 4 - Validation:** 5-step deep diligence research powered by Claude with web search. Covers market validation, competitive landscape, technical feasibility, team background, and financial model stress testing.
5. All three Affinity lists (AcceleratorBot, DealBot, StealthBot) are synced into Supabase.
6. Pipeline status and evaluation links are written back to Affinity records.

## Tech Stack

| Component  | Detail                                    |
|------------|-------------------------------------------|
| Language   | TypeScript (Next.js 14)                   |
| LLM        | Claude Sonnet 4.6 with web search         |
| Database   | Supabase PostgreSQL (companies + evals)   |
| Deployment | Vercel at bv-pipeline.vercel.app          |

- Frontend: Tailwind CSS

## Affinity Integration

- **Lists:** All three -- 324289 (AcceleratorBot), 169980 (DealBot), 335121 (StealthBot)
- **Reads:** Company records, scores, and metadata from all three lists; syncs into Supabase.
- **Writes:** Pipeline stage status and evaluation report links back to Affinity.

## Data Sources

- Affinity (all three bot lists synced into Supabase)
- Web research via Claude with web search (Stages 1 and 4)
- User-uploaded pitch deck PDFs (Stage 2)
- User-pasted call notes (Stage 3)

## How to Access / Use

Web application at [https://bv-pipeline.vercel.app](https://bv-pipeline.vercel.app).

## How to Trigger Manually

1. Go to the repository on GitHub.
2. Click the **Actions** tab.
3. Select the Affinity sync workflow from the left sidebar.
4. Click **Run workflow** (workflow_dispatch) to manually trigger a sync of Affinity data into Supabase.

## Key Configuration

| Variable                  | Location     | Purpose                          |
|---------------------------|--------------|----------------------------------|
| ANTHROPIC_API_KEY         | Vercel Env   | Claude Sonnet 4.6 API access     |
| SUPABASE_URL              | Vercel Env   | Supabase project URL             |
| SUPABASE_SERVICE_ROLE_KEY | Vercel Env   | Supabase admin access            |
| AFFINITY_API_KEY          | Vercel Env   | Sync and write-back to Affinity  |

## Known Limitations

- No authentication; anyone with the URL can access the dashboard.
- Affinity sync is not real-time; data may lag behind the latest Affinity state.
- Stage 4 deep diligence can be slow due to multiple sequential web searches.
- PDF parsing quality depends on deck formatting; scanned/image-only decks may not extract well.
