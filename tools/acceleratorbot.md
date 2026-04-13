# AcceleratorBot

Scrapes 30+ top accelerator programs and uses AI to score and push high-potential startups to Affinity.

## How It Works

1. Crawls 30+ accelerator websites (YC, IndieBio, MIT Delta V, Plug and Play, etc.) using crawl4ai with Playwright for JavaScript-rendered pages.
2. Extracts company names, descriptions, and cohort information from each accelerator's portfolio page.
3. Claude Haiku 4.5 classifies each company against BV's three investment themes and assigns a score from 1-10.
4. Companies scoring 6 or higher are enriched with founder contact info via the Hunter.io API.
5. Enriched, high-scoring companies are pushed to the Affinity list.
6. Post-push cleanup removes companies founded before 2020 or with more than $10M in funding.

## Tech Stack

| Component  | Detail                        |
|------------|-------------------------------|
| Language   | Python 3.13                   |
| LLM        | Claude Haiku 4.5 (Anthropic)  |
| Database   | None (JSON state files)       |
| Deployment | GitHub Actions                |

## Affinity Integration

- **List:** 324289
- **Reads:** Existing organizations on the list to avoid duplicates.
- **Writes:** New organizations with name, description, theme scores, founder contact info, and source accelerator.

## Data Sources

- 30+ accelerator websites (defined in `accelerators.json`)
- Hunter.io for founder email enrichment
- Scoring rubrics from `bv-rubrics` repo (company-scoring.yaml)

## How to Access / Use

Runs automatically via GitHub Actions every Wednesday at 10am CST.

## How to Trigger Manually

1. Go to the repository on GitHub.
2. Click the **Actions** tab.
3. Select the accelerator scrape workflow from the left sidebar.
4. Click **Run workflow** (workflow_dispatch).
5. Select the branch (usually `main`) and click the green **Run workflow** button.

## Key Configuration

| Variable           | Location       | Purpose                          |
|--------------------|----------------|----------------------------------|
| ANTHROPIC_API_KEY  | GitHub Secrets | Claude Haiku 4.5 API access      |
| AFFINITY_API_KEY   | GitHub Secrets | Push companies to Affinity       |
| HUNTER_API_KEY     | GitHub Secrets | Founder email enrichment         |

- **Config file:** `accelerators.json` defines all tracked accelerator programs and their URLs.
- **Budget cap:** $40/month on the Anthropic account.
- **Package manager:** uv

## Known Limitations

- Accelerator websites change layouts frequently, requiring crawl config updates.
- $40/month Anthropic budget cap may limit throughput if many new companies appear at once.
- Hunter.io free tier has limited lookups per month.
- JSON state files are not shared across runners; duplicate detection relies on Affinity list state.
