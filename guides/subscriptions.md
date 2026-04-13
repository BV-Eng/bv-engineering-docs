# Subscriptions & Billing Summary

Last updated: April 13, 2026

---

## Monthly Cost Overview

| Service | Monthly Cost | Billed To | Used By | Notes |
|---------|-------------|-----------|---------|-------|
| **Anthropic (Claude Code)** | $100 | eng account | Development & maintenance | Max plan for Claude Code CLI |
| **Anthropic (API credits)** | Variable | eng account | AcceleratorBot, DealBot, StealthBot, BV Pipeline, Raisebot | Prepaid credits loaded. **Set up auto-billing with spend limits.** AcceleratorBot self-caps at $40/mo. |
| **Vercel** | $20 | eng account | BV Pipeline, NewsBot, Raisebot | Pro plan. Hosts 3 web apps. |
| **PhantomBuster** | $159 | eng account | DealBot, StealthBot, PeopleBot | Growth plan. LinkedIn scraping. |
| **Hunter.io** | $49 | Rick's account | AcceleratorBot | Starter plan. Email enrichment. May switch to RocketReach. |
| **Supabase** | Free | — | BV Pipeline, Raisebot | Free tier. 2 PostgreSQL databases. Monitor usage — upgrade to Pro ($25/mo) if you hit limits. |
| **Upstash Redis** | Free | — | NewsBot | Free tier. Feed configs and newsletter storage. |
| **GitHub (BV-Eng org)** | Free | — | All repos | Free organization. GitHub Actions minutes included. |

### Total: ~$328/month + API usage

---

## Anthropic API Usage Breakdown

| Tool | Model | Estimated Monthly Usage | Notes |
|------|-------|------------------------|-------|
| AcceleratorBot | Claude Haiku 4.5 | ~$2-5 | Self-imposed $40/mo cap. Tracked in `state/llm_usage.json`. |
| StealthBot | Claude Sonnet 4.6 | ~$5-15 | 10 profiles/run, 4 runs/week. Low token volume per call. |
| DealBot | Claude Haiku 4.5 | ~$2-5 | Batches of 5 companies per call. |
| BV Pipeline | Claude Sonnet 4.6 + web search | Variable | Depends on how many evals team runs. Sonnet is more expensive. |
| Raisebot | Claude Haiku 4.5 | Minimal | Only fires on AI match queries. Very cheap model. |

**Action needed:** Set up auto-billing on the Anthropic account with a monthly spend limit. Recommended limit: $200/month to start (covers all tools with headroom). Monitor for a month, then adjust.

To set spend limits: https://console.anthropic.com → Settings → Billing → Spend limits

---

## Service-by-Service Details

### Anthropic
- **Account:** eng@better.vc
- **Console:** https://console.anthropic.com
- **What to watch:** API credit balance, monthly usage trends
- **If credits run out:** Bots will fail silently (API errors). AcceleratorBot tracks this in `state/llm_usage.json` and stops when budget is hit. Other tools will throw errors in GitHub Actions logs or Vercel function logs.

### Vercel
- **Account:** eng@better.vc
- **Dashboard:** https://vercel.com/dashboard
- **What to watch:** Build minutes, serverless function invocations, bandwidth
- **Pro plan includes:** 100GB bandwidth, 1000 build minutes, 1M function invocations
- **Risk:** BV Pipeline eval routes can run up to 5 minutes (deep diligence). This consumes serverless execution time. Heavy usage could approach limits.

### PhantomBuster
- **Dashboard:** https://phantombuster.com
- **Growth plan:** 500 min execution time, 5 phantoms, 100 phantom slots
- **Used by:**
  - DealBot: LinkedIn profile scraping (phantom "Dealbot Profile Scrape")
  - StealthBot: S3-hosted CSV of scraped LinkedIn profiles
  - PeopleBot: Sales Navigator Search Export phantom
- **What to watch:** Execution minutes remaining, phantom container status
- **Risk:** Phantoms can break if LinkedIn changes their UI. PhantomBuster usually fixes this, but there may be downtime.

### Hunter.io
- **Account:** Rick's personal account
- **Plan:** Starter ($49/mo) — 500 searches/month
- **Used by:** AcceleratorBot only (contact enrichment for founder emails)
- **Alternative considered:** RocketReach (may offer better coverage)
- **Action:** If switching providers, update `src/utils/hunter_enrichment.py` in the acceleratorbot repo and swap the `HUNTER_API_KEY` GitHub secret.

### Supabase
- **Free tier limits:** 500MB database, 2GB bandwidth, 50MB file storage
- **BV Pipeline tables:** `companies` (~hundreds of rows) and `evals` (~tens of rows) — well within limits
- **Raisebot tables:** `investors` (~2000 rows), `people`, `shortlists` — within limits
- **When to upgrade:** If either database exceeds 500MB or you need connection pooling beyond 20 connections

### Upstash Redis
- **Free tier limits:** 10,000 commands/day, 256MB storage
- **NewsBot usage:** Feed configs, saved articles, newsletters (up to 1000 items) — well within limits
- **When to upgrade:** Very unlikely to need it with current usage patterns

---

## If You Cancel a Service

| If you cancel... | What breaks |
|-----------------|-------------|
| Anthropic API | ALL AI-powered tools stop: AcceleratorBot, DealBot, StealthBot, BV Pipeline evals, Raisebot AI matching |
| Vercel | BV Pipeline, NewsBot, Raisebot all go offline |
| PhantomBuster | LinkedIn scraping stops for DealBot founders, StealthBot enrichment, PeopleBot |
| Hunter.io | AcceleratorBot still works but won't find founder emails |
| Supabase | BV Pipeline and Raisebot lose their databases (data loss!) |
| Upstash | NewsBot loses feed configs and saved articles (falls back to local JSON files) |
| GitHub | Everything breaks — all code and automation lives here |
