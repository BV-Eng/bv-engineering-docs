# Maintenance Guide for Future Claude Code Instances

You are maintaining a suite of tools for Better Ventures (BV), a venture capital fund. This guide tells you everything you need to operate, debug, and extend these systems.

---

## Critical Rules

- **Git identity**: ALWAYS use `eng@better.vc` as the git email. Vercel deploys are configured to accept only this identity. Set with:
  ```bash
  git config user.email "eng@better.vc"
  git config user.name "Better Ventures Bot"
  ```
- **Vercel auto-deploys**: All web apps auto-deploy from the `main` branch. Just push and Vercel handles the rest. Do not manually trigger deploys.
- **GitHub Actions**: All bots run via GitHub Actions. Secrets are stored in each repo's Settings > Secrets and variables > Actions.
- **Rubrics are centralized**: Never duplicate scoring logic in individual bot repos. All scoring rubrics live in `BV-Eng/bv-rubrics` as YAML files. All bots fetch from there at runtime. Edit only in that repo.

---

## API Keys Inventory

| Service | Where It Lives | Used By |
|---------|---------------|---------|
| ANTHROPIC_API_KEY | GitHub Secrets (acceleratorbot, dealbot, stealthbot), Vercel (bv-pipeline, raisebot) | Claude API calls (all tools) |
| AFFINITY_API_KEY | GitHub Secrets (all bots), Vercel (bv-pipeline) | Affinity CRM |
| HUNTER_API_KEY | GitHub Secrets (acceleratorbot) | Contact enrichment |
| PHANTOMBUSTER_API_KEY | GitHub Secrets (dealbot, stealthbot, peoplebot) | LinkedIn scraping |
| SUPABASE_URL + SUPABASE_SERVICE_ROLE_KEY | Vercel (bv-pipeline, raisebot) | PostgreSQL |
| KV_REST_API_URL + KV_REST_API_TOKEN | Vercel (newsbot) | Upstash Redis |

---

## Common Maintenance Tasks

### Add a new accelerator to AcceleratorBot

1. Edit `accelerators.json` in the acceleratorbot repo.
2. Add an entry with `name`, `mode` ("batch" or "directory"), and `seed_urls`.
3. Commit and push. The next Wednesday run will include it.

### Update scoring rubrics

1. Edit the relevant YAML file in `BV-Eng/bv-rubrics`.
2. Commit and push. All bots pick up changes on their next run — no deploys needed.

### Trigger a bot run manually

1. Go to the repo on GitHub > Actions tab.
2. Select the workflow > click "Run workflow" button.
3. AcceleratorBot has options for `dry_run` and `override_cap`.
4. DealBot triggers automatically on CSV upload to the `input/` directory.

### Re-sync BV Pipeline with Affinity

1. Go to bv-pipeline.vercel.app/pipeline.
2. Click the "Sync from Affinity" button.
3. Wait for sync to complete (may take a few minutes due to API rate limiting).

### Add or change RSS feeds in NewsBot

- **Via UI:** Click "+" in the sidebar, paste an RSS URL or Google News URL.
- **Bulk changes:** Feed configs are stored in Upstash Redis under keys like `news-bot:feeds:wes`. Fallback JSON files live in the `data/` directory of the newsbot repo.

### Update investor data in Raisebot

1. Export from Affinity as CSV.
2. Go to raisebot-fawn.vercel.app/admin/import and upload.
3. Run enrichment scripts in `backend/enrichment/` for Affinity connection data.

### Upgrade the LLM model across all tools

All repos support a `CLAUDE_MODEL` environment variable. To upgrade:
- **Bots (AcceleratorBot, DealBot, StealthBot):** Add `CLAUDE_MODEL` to the repo's GitHub Secrets with the new model ID. If not set, the current default is used.
- **Web apps (BV Pipeline, Raisebot):** Add `CLAUDE_MODEL` to Vercel Environment Variables.
- Defaults: AcceleratorBot/DealBot/Raisebot use `claude-haiku-4-5-20251001`, StealthBot/BV Pipeline use `claude-sonnet-4-6`.

### Enable Slack failure alerts

Bot workflows (AcceleratorBot, DealBot, StealthBot) have Slack notification steps built in. To activate:
1. Set up a Slack incoming webhook for your desired channel.
2. Add the webhook URL as `SLACK_WEBHOOK_URL` in each repo's GitHub Secrets.
3. Alerts will fire automatically when any workflow fails.

---

## Common Failure Modes

### AcceleratorBot $40/month budget
The LLM spend is tracked in `state/llm_usage.json`. If the monthly budget is exceeded, the bot skips LLM calls silently. The counter resets at the start of each month.

### Affinity API rate limits
All tools have built-in rate limiting and exponential backoff. If you are still hitting limits, reduce batch sizes in the relevant config.

### PhantomBuster phantom expiry
Phantoms have limited container minutes per month. If scraping stops working, check the PhantomBuster dashboard for expired or paused phantoms.

### Vercel function timeout
BV Pipeline evaluation routes have a 300-second (5-minute) timeout. Stage 4 (Validation/deep diligence) can approach this limit on complex companies. If it times out, retry.

### Supabase connection limits
Raisebot has a max of 3 connections. BV Pipeline uses the service role key. Do not open additional persistent connections.

---

## Architecture Quick Reference

- **Bots** are stateless Python or Node.js scripts that run on GitHub Actions on a schedule or trigger.
- **Web apps** are Next.js applications deployed on Vercel.
- **Affinity** is the central system of record for all deals and relationships.
- **Scoring rubrics** are YAML files in the `bv-rubrics` repo, fetched at runtime by all bots.
- **Data flow**: External Sources > Bots > Affinity > BV Pipeline.

The bots source companies from the outside world, score them, and push them into Affinity. The BV Pipeline dashboard pulls from Affinity and provides the team with a multi-stage evaluation workflow. Raisebot and PeopleBot serve portfolio support functions. NewsBot is standalone and sources from RSS and newsletters.
