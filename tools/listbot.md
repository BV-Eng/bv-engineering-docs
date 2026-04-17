# ListBot

Ad-hoc company list scoring tool. Upload any company list from any source, extract companies, score with BV themes, scrape founder LinkedIns via PhantomBuster, score founders, push to Affinity.

- **Web App:** [listbot-three.vercel.app](https://listbot-three.vercel.app)
- **Repo:** [BV-Eng/listbot](https://github.com/BV-Eng/listbot)

## How It Works

1. Choose an input method:
   - **Spreadsheet upload** (CSV/XLSX) — auto-detects columns via alias matching
   - **HTML paste** — block-based CMS extraction (Deepchecks, Webflow sites, accelerator pages)
   - **Plain text** — Claude extracts structured company data from unstructured text
   - **URL** — Claude web-searches and extracts from a live page
2. Review extracted data in a preview table. Re-map columns if needed.
3. Choose scoring mode (BV default themes, supplement, or custom) and founder mode (skip, from spreadsheet, separate file, or web search).
4. Score companies with Claude. For founder mode:
   - Updates a GitHub Gist with founder LinkedIn URLs
   - Launches PhantomBuster phantom for LinkedIn profile scraping
   - Polls PB with real progress tracking, filters results to current run
   - Scores founders using canonical `bv-rubrics/founder-scoring.yaml`
5. Download results as Excel or push to Affinity (AcceleratorBot list 324289 or DealBot list 169980).

## Tech Stack

| Component  | Detail                                   |
|------------|------------------------------------------|
| Framework  | Next.js 14, TypeScript                   |
| Deployment | Vercel                                   |
| LLM        | Claude Haiku 4.5 (fast), Sonnet 4.6 (complex) |
| Rubrics    | Fetched from BV-Eng/bv-rubrics           |
| LinkedIn   | PhantomBuster Profile Scraper            |
| Auth       | Google OAuth restricted to @better.vc    |

## HTML Extraction

For CMS-driven pages (Deepchecks, Webflow), ListBot uses block-based extraction:
- Splits HTML on per-record container class (e.g. `class="deal-item"`)
- Extracts company name from `class="company-name"` (most reliable)
- Extracts other fields (description, valuation, sector, region) per-block from CMS field selectors
- Derives company URL from founder email domain when no explicit website link exists

## Env Vars

| Variable                | Purpose                          |
|-------------------------|----------------------------------|
| ANTHROPIC_API_KEY       | Claude scoring                   |
| PHANTOMBUSTER_API_KEY   | LinkedIn scraping                |
| PHANTOMBUSTER_PHANTOM_ID| PB phantom ID                    |
| GITHUB_TOKEN            | Gist updates                     |
| GIST_ID                 | Gist for PB input CSV            |
| GOOGLE_CLIENT_ID        | OAuth                            |
| GOOGLE_CLIENT_SECRET    | OAuth                            |
| NEXTAUTH_SECRET         | Session encryption               |

## Affinity Push

- Supports two target lists: AcceleratorBot (324289) or DealBot (169980)
- Maps all available fields to Affinity field IDs
- Investor auto-assignment: Rick (Climate), Wes (Health), Lyndsey (Workforce)
- Rate-limited: 10 req/sec, max 200 per push
