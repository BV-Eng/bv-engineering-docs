# Scoring Rubrics

Central YAML configuration files that define how AcceleratorBot, DealBot, and StealthBot evaluate and route companies and founders.

## How It Works

1. Four YAML files define the scoring criteria, investor routing rules, and prestige signals used across all scoring bots.
2. At runtime, AcceleratorBot, DealBot, and StealthBot fetch these YAML files from the `bv-rubrics` repo.
3. Company scoring evaluates startups on three themes (Climate, Health, Workforce) each scored 1-10, plus a core business viability test and exclusion rules.
4. Founder scoring assigns a holistic 1-10 score based on specific evaluation criteria (domain expertise, track record, team composition).
5. Investor routing maps theme scores to BV team owners: Rick for Climate, Wes for Health, Lyndsey for Workforce, with keyword matching and Affinity person IDs.
6. The prestigious lists file flags borderline companies that have backing from top VCs, accelerators, or angels, giving them a scoring boost.

## Tech Stack

| Component  | Detail                         |
|------------|--------------------------------|
| Language   | YAML (configuration only)      |
| LLM        | N/A (consumed by other bots)   |
| Database   | None                           |
| Deployment | None -- committed to GitHub    |

## Affinity Integration

- **Lists:** Not directly integrated. Consumed by bots that write to lists 324289, 169980, and 335121.
- **Reads:** Nothing directly.
- **Writes:** Nothing directly. The `investors.yaml` file contains Affinity person IDs for owner routing.

## Data Sources

- Manually curated by the BV team.
- `company-scoring.yaml` -- theme definitions, scoring criteria, exclusion rules.
- `founder-scoring.yaml` -- founder evaluation criteria and scoring rubric.
- `investors.yaml` -- owner routing rules with keywords and Affinity person IDs (Rick, Wes, Lyndsey).
- `prestigious-lists.yaml` -- top VCs, accelerators, and angel investors for borderline flagging.

## How to Access / Use

Edit the YAML files directly on GitHub at [github.com/BV-Eng/bv-rubrics](https://github.com/BV-Eng/bv-rubrics). All bots pick up changes on their next run.

## How to Trigger Manually

No manual trigger needed. To update scoring behavior:

1. Go to [github.com/BV-Eng/bv-rubrics](https://github.com/BV-Eng/bv-rubrics).
2. Edit the relevant YAML file.
3. Commit the change to `main`.
4. All consuming bots (AcceleratorBot, DealBot, StealthBot) will use the updated rubrics on their next scheduled or manually triggered run.

## Key Configuration

| File                    | Purpose                                              |
|-------------------------|------------------------------------------------------|
| company-scoring.yaml    | 3-theme scoring (Climate, Health, Workforce) 1-10    |
| founder-scoring.yaml    | Holistic founder scoring 1-10                        |
| investors.yaml          | Owner routing: Rick/Wes/Lyndsey with keywords + IDs  |
| prestigious-lists.yaml  | Top VCs/accelerators/angels for borderline boosting   |

- **Repo:** github.com/BV-Eng/bv-rubrics
- No environment variables or secrets needed.

## Known Limitations

- Changes are not versioned with semantic releases; a bad YAML edit can silently break scoring across all bots.
- No validation CI; YAML syntax errors are only caught at bot runtime.
- Prestigious lists require manual curation and can become outdated.
- Keyword-based investor routing is coarse; companies spanning multiple themes may be misrouted.
