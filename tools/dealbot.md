# DealBot

Web app for scoring PitchBook exports. Upload companies + people, score with Claude, curate via preview, auto-scrape founders via PhantomBuster, push to Affinity.

- **Web App:** [dealbot-web.vercel.app](https://dealbot-web.vercel.app)
- **Backend Repo:** [BV-Eng/dealbot](https://github.com/BV-Eng/dealbot) (Python + GitHub Actions)
- **Web Repo:** [BV-Eng/dealbot-web](https://github.com/BV-Eng/dealbot-web) (Next.js on Vercel)

## How It Works

1. Upload two PitchBook exports (companies + people) at dealbot-web.vercel.app.
2. Web app commits files to a per-run branch and triggers a GitHub Actions workflow.
3. Claude Haiku 4.5 scores each company on Climate (1-10), Health (1-10), Workforce (1-10).
4. Web app shows a preview with two tabs: **Recommended** (above threshold) and **Excluded** (below). User curates via checkboxes.
5. User clicks "Push to Affinity" — triggers stage 2 workflow:
   - Pushes selected companies to Affinity list 169980
   - Matches founders from the people file by company name
   - Updates a GitHub Gist with founder LinkedIn URLs
   - Launches PhantomBuster "[Dealbot] Profile Scrape" phantom (ID: 1093960771064458)
   - Polls PB with two-phase approach (waits for our container, then polls for done)
   - Scores founders using canonical `bv-rubrics/founder-scoring.yaml` holistic rubric (1-10 + reasoning)
   - Pushes Founder Score Total + Reasoning + LinkedIn URL to Affinity
6. Owner auto-assignment: Rick (Climate), Wes (Health), Lyndsey (Workforce) based on highest theme score.

## Tech Stack

| Component  | Detail                               |
|------------|--------------------------------------|
| Backend    | Python 3.11 (GitHub Actions)         |
| Web App    | Next.js 14, TypeScript (Vercel)      |
| LLM        | Claude Haiku 4.5 (Anthropic)         |
| Rubrics    | Fetched from BV-Eng/bv-rubrics       |
| LinkedIn   | PhantomBuster Profile Scraper        |
| CI         | 9 smoke tests on every push to main  |

## Affinity Integration

- **List:** 169980
- **Company fields:** Description, scores (Climate/Health/Workforce), BV Fit Score, keywords, investors, owners
- **Founder fields:** Founder Score Total, Founder Score Reasoning, Founder LinkedIn
- Existing Affinity data is not overwritten (skip-if-exists for founder fields)

## Env Vars

| Variable              | Location         | Purpose                          |
|-----------------------|------------------|----------------------------------|
| ANTHROPIC_API_KEY     | GitHub Secrets   | Claude scoring                   |
| AFFINITY_API_KEY      | GitHub Secrets   | Push to Affinity                 |
| PHANTOMBUSTER_API_KEY | GitHub Secrets   | LinkedIn founder scraping        |
| GIST_PAT              | GitHub Secrets   | Update GitHub Gist for PB input  |
| DEALBOT_GIST_ID       | GitHub Variables | Gist ID for PB input CSV         |
| GITHUB_PAT            | Vercel env       | Web app → GitHub API             |

## Error Recovery

- **Retry button** in web UI: merges latest main into run branch + re-triggers workflow.
- **URL resume:** `?runId=...` survives page refresh — reloads state from the run branch.
- **Branch cleanup:** auto-deletes per-run branch when starting a new run.

## Known Behaviors

- PB two-phase polling waits for container acknowledgment before accepting "finished" (prevents stale-status race).
- Companies already in Affinity list are skipped (status: existing).
- Founder fields with existing values are not overwritten.
- PB Profile Scraper columns are `linkedin`-prefixed (e.g. `linkedinHeadline`, `linkedinJobTitle`). The pipeline normalizes these before scoring.
