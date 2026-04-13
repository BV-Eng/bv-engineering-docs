# Critical Configuration Reference

---

## Git Identity

```bash
git config user.email "eng@better.vc"
git config user.name "Better Ventures Bot"
```

Required for all repos. Vercel deploys reject other identities.

---

## Vercel Projects

| App | Vercel Project | Domain | Auto-Deploy Branch |
|-----|---------------|--------|-------------------|
| BV Pipeline | bv-pipeline | bv-pipeline.vercel.app | main |
| NewsBot | news-bot (may need rename) | news-bot-4nee.vercel.app | main |
| Raisebot | raisebot (may need rename) | raisebot-fawn.vercel.app | main |
| PeopleBot | Not deployed | — | — |

---

## Affinity Lists

| List | ID | Type | Bot |
|------|-----|------|-----|
| DealBot | 169980 | Organizations | DealBot (pitchbook-pipeline) |
| AcceleratorBot | 324289 | Organizations | AcceleratorBot |
| Stealth | 335121 | Persons | StealthBot |

---

## BV Team Members (Affinity Person IDs)

| Person | ID | Themes |
|--------|-----|--------|
| Rick Moss | 2999193 | Climate, Sustainability, Electrification |
| Wes Selke | 2999192 | Health, Food, Nutrition, Bioeconomy |
| Lyndsey Boucherle | 5794280 | Workforce, Education, Circular Economy |
| Katie | 116247435 | — |

---

## GitHub Actions Schedules

| Bot | Schedule | Cron |
|-----|----------|------|
| AcceleratorBot | Wednesdays 10am CST | `0 16 * * 3` |
| StealthBot (enrich) | Mon/Wed/Thu/Fri 10am PST | `0 18 * * 1,3,4,5` |
| StealthBot (sync) | On Google Sheet update | repository_dispatch |
| DealBot | On CSV upload to input/ | push trigger |

---

## LLMs in Use

| Tool | Model | Provider |
|------|-------|----------|
| AcceleratorBot | claude-haiku-4-5-20251001 | Anthropic |
| DealBot | gpt-4o-mini | OpenAI |
| StealthBot | claude-sonnet-4-6 | Anthropic |
| BV Pipeline | claude-sonnet-4-20250514 | Anthropic |
| Raisebot | claude-3-haiku-20240307 | Anthropic |
| NewsBot | None | — |
| PeopleBot | None | — |
