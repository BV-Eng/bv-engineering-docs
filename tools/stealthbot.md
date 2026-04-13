# StealthBot

Syncs stealth-mode founder outreach from Google Sheets to Affinity and enriches contacts with AI-scored profiles.

## How It Works

### Sync Workflow
1. A Google Apps Script in the outreach Google Sheet fires a `repository_dispatch` event to GitHub.
2. The sync workflow reads new rows from the Sheet and creates corresponding Affinity person contacts.
3. Contact details (name, email, company, notes) are written to the Affinity persons list.

### Enrich Workflow
1. Fetches a PhantomBuster CSV containing scraped LinkedIn profile data for contacts on the list.
2. Claude Sonnet 4.6 scores each founder on a 1-10 scale using the scoring rubrics.
3. Writes the score, investment thesis alignment, and assigned owner back to the Affinity person record.

## Tech Stack

| Component  | Detail                        |
|------------|-------------------------------|
| Language   | Node.js 20                    |
| LLM        | Claude Sonnet 4.6 (Anthropic) |
| Database   | None                          |
| Deployment | GitHub Actions                |

- No npm dependencies except `js-yaml`.

## Affinity Integration

- **List:** 335121 (persons list, not organizations)
- **Reads:** Existing person records to avoid duplicates during sync; existing fields during enrich.
- **Writes:** New person contacts (sync); score, thesis match, and owner assignment (enrich).

## Data Sources

- Google Sheets outreach tracker (via Google Apps Script dispatch)
- PhantomBuster LinkedIn scrapes (CSV)
- Scoring rubrics from `bv-rubrics` repo (founder-scoring.yaml)

## How to Access / Use

Runs automatically via GitHub Actions:
- **Sync:** Triggered on `repository_dispatch` from Google Apps Script.
- **Enrich:** Runs 4x/week on Monday, Wednesday, Thursday, and Friday at 10am Pacific.

## How to Trigger Manually

1. Go to the repository on GitHub.
2. Click the **Actions** tab.
3. Select either the sync or enrich workflow from the left sidebar.
4. Click **Run workflow** (workflow_dispatch).
5. Select the branch (usually `main`) and click the green **Run workflow** button.

## Key Configuration

| Variable              | Location       | Purpose                          |
|-----------------------|----------------|----------------------------------|
| AFFINITY_API_KEY      | GitHub Secrets | Read/write Affinity contacts     |
| ANTHROPIC_API_KEY     | GitHub Secrets | Claude Sonnet 4.6 scoring        |
| PHANTOMBUSTER_API_KEY | GitHub Secrets | LinkedIn profile enrichment      |

## Known Limitations

- Google Apps Script dispatch requires the Sheet to be properly configured; changes to the Sheet structure can break sync.
- Persons list (not orgs) means company-level dedup is handled differently than other bots.
- PhantomBuster scrape quality varies for profiles with limited public information.
- No database or state file; relies entirely on Affinity as the source of truth.
