# System Ecosystem

---

```mermaid
graph TB
    subgraph "Data Sources"
        ACC[30+ Accelerator Websites]
        PB[PitchBook Exports]
        LI[LinkedIn / PhantomBuster]
        GS[Google Sheets]
        RSS[RSS Feeds / Google News]
        ZAP[Zapier / Newsletters]
    end

    subgraph "Scoring Rubrics"
        RUB[bv-rubrics<br/>YAML on GitHub]
    end

    subgraph "Deal Flow Bots"
        ABOT[AcceleratorBot<br/>Python + Claude Haiku 4.5<br/>GitHub Actions Weekly]
        DBOT[DealBot<br/>Python + GPT-4o-mini<br/>GitHub Actions on Upload]
        SBOT[StealthBot<br/>Node.js + Claude Sonnet 4.6<br/>GitHub Actions 4x/week]
    end

    subgraph "Central CRM"
        AFF[Affinity CRM<br/>3 Lists: DealBot, AcceleratorBot, Stealth]
    end

    subgraph "Deal Evaluation"
        PIPE[BV Pipeline Dashboard<br/>Next.js + Claude Sonnet 4<br/>Vercel + Supabase]
    end

    subgraph "Portfolio Tools"
        RAISE[Raisebot<br/>Next.js + Claude 3 Haiku<br/>Vercel + Supabase]
        PEOPLE[PeopleBot<br/>FastAPI + Next.js<br/>Not Deployed]
    end

    subgraph "Information"
        NEWS[NewsBot<br/>Next.js + Upstash Redis<br/>Vercel]
    end

    ACC --> ABOT
    PB --> DBOT
    LI --> SBOT
    LI --> DBOT
    LI --> PEOPLE
    GS --> SBOT
    RSS --> NEWS
    ZAP --> NEWS

    RUB -.->|fetched at runtime| ABOT
    RUB -.->|fetched at runtime| DBOT
    RUB -.->|fetched at runtime| SBOT

    ABOT -->|scores + companies| AFF
    DBOT -->|scores + companies| AFF
    SBOT -->|scores + founders| AFF

    AFF <-->|sync + writeback| PIPE
    AFF -.->|relationship data| RAISE
    AFF -.->|warmth data| PEOPLE

    PIPE -->|eval links| AFF
```

---

## How It All Fits Together

The BV engineering ecosystem has three layers: sourcing, evaluation, and portfolio support.

**Sourcing** happens through three bots that each watch a different channel. AcceleratorBot crawls 30+ accelerator websites weekly, looking for startups that match BV's investment themes. DealBot processes PitchBook CSV exports whenever they are uploaded. StealthBot monitors LinkedIn outreach from founders and scores them based on Google Sheet data enriched with PhantomBuster scrapes. All three bots pull their scoring criteria from the centralized bv-rubrics repository at runtime, ensuring consistent evaluation standards.

**Affinity CRM** sits at the center. Every bot pushes scored companies or founders into one of three Affinity lists. Affinity is the system of record — it holds deal status, owner assignments, notes, and relationship data. The BV Pipeline dashboard syncs bidirectionally with Affinity: it pulls companies in for evaluation and writes status updates and evaluation links back.

**Evaluation** happens in the BV Pipeline dashboard. The team moves deals through four stages — Quick Screen, Deck Eval, Post-Call, and Validation — with AI assistance at each stage. Each stage adds more depth to the analysis.

**Portfolio support** tools operate independently. Raisebot helps portfolio companies find investors by matching company profiles against an investor database enriched with Affinity relationship data. NewsBot aggregates RSS feeds, Google News, and newsletter content into personalized dashboards for each team member. PeopleBot (not yet deployed) is designed for executive recruiting with LinkedIn data and Affinity warmth scoring.
