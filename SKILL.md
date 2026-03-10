---
name: builder-data
description: Query builder reputation data via Talent Protocol API. Get Builder Rank, verify humans, resolve identities (Twitter/Farcaster/GitHub/wallet), search by location/country, get credentials, and enrich with GitHub data.
---

# Builder Data

Query professional data from [Talent](https://talent.app) - a platform that tracks builders

## When to use

- Find verified developers by location, skills, or identity (Twitter/GitHub/Farcaster/wallet)
- Check builder reputation (ranks by default, scores only when asked)
- Map Twitter/Farcaster accounts to wallet addresses
- Verify human identity from a wallet
- Search credentials (earnings, contributions, hackathons, contracts, etc.)
- Check the projects each builder is shipping

## Required Credentials

| Variable | Required | Description | Get it at |
|----------|----------|-------------|-----------|
| `TALENT_API_KEY` | **Yes** | Talent API key | https://talent.app/~/settings/api |
| `GITHUB_TOKEN` | No | GitHub PAT for higher rate limits (60/hr → 5,000/hr) | https://github.com/settings/tokens |

**Base URL:** `https://api.talentprotocol.com`

**Auth header:** `X-API-KEY: $TALENT_API_KEY`

## Workflow

1. **Identify the task** — what does the user want?
2. **Pick the right endpoint** — see [endpoints.md](references/endpoints.md)
3. **Construct the API call** — see [use-cases.md](references/use-cases.md)
4. **Disambiguate** — identity searches return impersonators. Pick the profile with the highest `builder_score`. Never blindly use the first result. If no result has a meaningful score, report that no confident match was found.
5. **For GitHub data** — resolve GitHub username via `/accounts`, then follow [github-enrichment.md](references/github-enrichment.md)
6. **Present results** — default to `rank_position`; only show `points` when explicitly asked. If `rank_position` is `null`, fall back to `points`.
7. **If 0 results** — broaden: try a different identity type

## Endpoints

| Endpoint | Purpose |
|----------|---------|
| `/search/advanced/profiles` | Search by identity, tags, rank, verification |
| `/profile` | Get profile by ID |
| `/accounts` | Get connected wallets, GitHub, socials |
| `/socials` | Get social profiles + bios |
| `/credentials` | Get data points (earnings, hackathons, etc.) |
| `/scores` | Get ranks/scores. Slugs: `builder_score`, `builder_score_2025`, `creator_score` |
| `/human_checkmark` | Check if human-verified. Response field: `humanity_verified` |
| `/data_points` | List available data point slugs |
| `/data_issuers_meta` | List all data issuers and credential slugs |

## Critical Rules

- **Ranks by default.** Show `builder_score.rank_position`. Only show `points` when explicitly asked. `rank_position` is often `null` — fall back to `points`.
- **Default to `builder_score` slug.** API also returns `builder_score_2025` (legacy) and `creator_score`.
- **Location filtering is broken.** Both GET param and POST `customQuery` fail (302). Search broadly and filter client-side by `standardized_location` / `location`.
- **Identity searches return impersonators.** Disambiguate by picking the profile with the highest `builder_score`. If no result has a meaningful score, report that no confident match was found.
- **URL encoding:** `[` = `%5B`, `]` = `%5D`, Space = `%20`
- **Max 25 results per page.** API rejects `per_page` values above 25. Paginate with `page` param for broader searches.

## Alternative: Talent Agent CLI

If `talent-agent` is installed (`npm install -g talent-agent`), it provides natural language search without manual API calls. It can also run as an MCP server (`talent-agent --serve`) for use in Cursor, Claude, and other MCP-compatible clients.

## References

- [endpoints.md](references/endpoints.md) — Endpoint parameters and response schemas
- [use-cases.md](references/use-cases.md) — Common patterns
- [github-enrichment.md](references/github-enrichment.md) — GitHub API enrichment flow
