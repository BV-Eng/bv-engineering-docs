# DealBot

Scores PitchBook deal exports and LinkedIn-scraped founders, then pushes qualified companies to Affinity with auto-assigned owners.

## How It Works

1. A PitchBook CSV or XLSX export is placed in the `input/` folder (manually or via push).
2. Claude Haiku 4.5 scores each company across three themes: Climate (1-10), Health (1-10), and Workforce (1-10).
3. Companies scoring 5 or higher on any theme pass the filter.
4. PhantomBuster LinkedIn scrapes (via a GitHub Gist intermediary) provide founder profile data.
5. Founders are scored separately based on their LinkedIn profiles.
6. Passing companies and scored founders are pushed to the Affinity list.
7. Owners are auto-assigned based on highest theme score: Rick for Climate, Wes for Health, Lyndsey for Workforce.

## Tech Stack

| Component  | Detail                     |
|------------|----------------------------|
| Language   | Python 3.11                |
| LLM        | Claude Haiku 4.5 (Anthropic) |
| Database   | None (file-based)          |
| Deployment | GitHub Actions             |

## Affinity Integration

- **List:** 169980
- **Reads:** Existing organizations to avoid duplicates.
- **Writes:** New organizations with name, description, theme scores, founder scores, and assigned owner (Rick/Wes/Lyndsey).

## Data Sources

- PitchBook CSV/XLSX exports (placed in `input/` folder)
- PhantomBuster LinkedIn scrapes (fetched via GitHub Gist intermediary)
- Scoring rubrics from `bv-rubrics` repo (company-scoring.yaml, founder-scoring.yaml)

## How to Access / Use

Runs automatically via GitHub Actions when a CSV is pushed to the `input/` folder.

## How to Trigger Manually

1. Go to the repository on GitHub.
2. Click the **Actions** tab.
3. Select the deal scoring workflow from the left sidebar.
4. Click **Run workflow** (workflow_dispatch).
5. Select the branch and click the green **Run workflow** button.
6. Alternatively, push a new CSV/XLSX file to the `input/` folder to trigger automatically.

## Key Configuration

| Variable              | Location       | Purpose                           |
|-----------------------|----------------|-----------------------------------|
| ANTHROPIC_API_KEY     | GitHub Secrets | Claude Haiku 4.5 scoring          |
| AFFINITY_API_KEY      | GitHub Secrets | Push companies to Affinity        |
| PHANTOMBUSTER_API_KEY | GitHub Secrets | LinkedIn founder scraping         |

## Known Limitations

- Depends on PitchBook exports being manually generated and placed in the correct folder.
- PhantomBuster LinkedIn scraping is rate-limited and may fail for private profiles.
- GitHub Gist intermediary for PhantomBuster adds a fragile dependency.
- No database means no historical tracking of previously scored companies beyond Affinity dedup.
