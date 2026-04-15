# Better Ventures Engineering Documentation

Complete guide to BV's automated deal flow, portfolio tools, and information systems. Built 2025-2026.

**BV Engineering Hub (start here):** [bv-engineering-hub.vercel.app](https://bv-engineering-hub.vercel.app) — Central dashboard with live status, tool access, config editing, and password vault.

---

## Tool Quick Reference

| Tool Name | What It Does | Live URL | GitHub Repo | Runs On |
|-----------|-------------|----------|-------------|---------|
| AcceleratorBot | Scrapes 30+ accelerator batch pages, scores companies against BV thesis, pushes to Affinity | No web UI | [github.com/BV-Eng/acceleratorbot](https://github.com/BV-Eng/acceleratorbot) | GitHub Actions (weekly) |
| DealBot | Scores PitchBook company exports + founders against BV thesis, pushes to Affinity | No web UI | [github.com/BV-Eng/dealbot](https://github.com/BV-Eng/dealbot) | GitHub Actions (on CSV upload) |
| StealthBot | Scores LinkedIn outreach founders, assigns thesis fit + owner in Affinity | No web UI | [github.com/BV-Eng/stealthbot](https://github.com/BV-Eng/stealthbot) | GitHub Actions (4x/week) |
| BV Pipeline | 4-stage AI deal evaluation dashboard, syncs with all Affinity lists | [bv-pipeline.vercel.app](https://bv-pipeline.vercel.app) | [github.com/BV-Eng/bv-pipeline](https://github.com/BV-Eng/bv-pipeline) | Vercel |
| Scoring Rubrics | Shared YAML scoring criteria consumed by all bots | N/A | [github.com/BV-Eng/bv-rubrics](https://github.com/BV-Eng/bv-rubrics) | N/A (consumed via GitHub raw URL) |
| NewsBot | Personalized RSS/news aggregation dashboard per team member | [news-bot-4nee.vercel.app](https://news-bot-4nee.vercel.app) | [github.com/BV-Eng/newsbot](https://github.com/BV-Eng/newsbot) | Vercel |
| Raisebot | Investor database + AI matching for portfolio companies | [raisebot-fawn.vercel.app](https://raisebot-fawn.vercel.app) | [github.com/BV-Eng/raisebot](https://github.com/BV-Eng/raisebot) | Vercel |
| ListBot | Upload any company list, get AI-scored results with founder scoring + Affinity push | [listbot-three.vercel.app](https://listbot-three.vercel.app) | [github.com/BV-Eng/listbot](https://github.com/BV-Eng/listbot) | Vercel |
| PeopleBot | Executive recruiting via LinkedIn/PhantomBuster + Affinity warmth scoring | Not yet deployed | [github.com/BV-Eng/peoplebot](https://github.com/BV-Eng/peoplebot) | Not deployed |

---

## How It All Connects

The BV engineering stack forms a closed loop around the investment process. Three sourcing bots (AcceleratorBot, DealBot, StealthBot) continuously feed qualified companies into Affinity CRM. BV Pipeline then provides a structured, multi-stage AI evaluation layer on top of Affinity, enabling the team to move from raw deal to investment decision. Portfolio tools (Raisebot, PeopleBot) support companies post-investment, and NewsBot keeps the team current on market activity and portfolio news.

All bots share a single set of scoring rubrics (bv-rubrics) to ensure consistent thesis alignment across every sourcing channel.

For a full architecture narrative and data flow diagrams, see [SYSTEM-OVERVIEW.md](SYSTEM-OVERVIEW.md) and the `diagrams/` folder.

---

## For Venture Partners

Getting started with the tools, dashboard walkthroughs, and day-to-day usage guides:

- [Venture Partner Guide](guides/for-venture-partners.md)

---

## For Maintenance / Future Engineers

Technical onboarding, configuration references, and operational runbooks:

- [Working with Claude Code](guides/for-claude-code.md)
- [Critical Configuration Reference](guides/critical-config.md)
- [Subscriptions & Billing](guides/subscriptions.md)
