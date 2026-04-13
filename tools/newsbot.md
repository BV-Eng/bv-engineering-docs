# NewsBot

Personalized RSS and news aggregation dashboard for the BV team, portfolio companies, and newsletters.

## How It Works

1. RSS feeds and Google News searches are configured per team member (Rick, Wes, Lyndsey) based on their investment themes and interests.
2. Google News RSS feeds track 71 portfolio companies automatically.
3. Newsletters are ingested via Zapier, which posts to the `/api/newsletters` endpoint.
4. All feed items are cached in Upstash Redis for fast retrieval.
5. The web dashboard displays categorized news with per-person views, a PortCos tab, and a Newsletters tab.
6. Articles can be shared to Slack via webhook integration across 5 configured channels.

## Tech Stack

| Component  | Detail                                    |
|------------|-------------------------------------------|
| Language   | TypeScript (Next.js, latest)              |
| LLM        | None -- pure aggregation and display      |
| Database   | Upstash Redis                             |
| Deployment | Vercel at news-bot-4nee.vercel.app        |

- Frontend: Tailwind CSS

## Data Sources

- RSS feeds (curated per team member)
- Google News RSS (71 portfolio companies)
- Newsletters (ingested via Zapier webhook to `/api/newsletters`)

## How to Access / Use

Web application at [https://news-bot-4nee.vercel.app](https://news-bot-4nee.vercel.app).

## How to Trigger Manually

1. Go to the repository on GitHub.
2. Click the **Actions** tab.
3. Select the relevant workflow from the left sidebar (if applicable).
4. Click **Run workflow** (workflow_dispatch).
5. Feed refreshes also happen automatically on page load and via scheduled Vercel cron.

## Key Configuration

| Variable           | Location     | Purpose                    |
|--------------------|--------------|----------------------------|
| KV_REST_API_URL    | Vercel Env   | Upstash Redis connection   |
| KV_REST_API_TOKEN  | Vercel Env   | Upstash Redis auth token   |

- Zapier zap configured to POST newsletters to `/api/newsletters`.
- Slack webhook URLs configured for 5 channels.
- Portfolio company list and RSS feed URLs maintained in the codebase.

## Known Limitations

- No LLM summarization; articles are shown as-is from RSS feeds.
- Google News RSS can be unreliable for lesser-known portfolio companies.
- Newsletter ingestion depends on Zapier remaining configured and active.
- No authentication; anyone with the URL can access the dashboard.
- Redis cache has TTLs; very old articles drop off automatically.
