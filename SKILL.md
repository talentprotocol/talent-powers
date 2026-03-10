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

1. **Identify the task** — what does the user want? (identity lookup, search, credentials, GitHub enrichment)
2. **Pick the right endpoint** — see the endpoint table below, then read [endpoints.md](references/endpoints.md) for full request/response details
3. **Construct the API call** — use the patterns in [use-cases.md](references/use-cases.md) for common scenarios
4. **For GitHub data** — resolve the GitHub username via `/accounts` first, then follow [github-enrichment.md](references/github-enrichment.md)
5. **Present results** — default to `rank_position`; only include `points` when the user explicitly asks for scores
6. **If 0 results** — broaden the search: remove `human_checkmark`, use a less specific location regex, or try a different identity type

## Endpoints

| Endpoint | Purpose |
|----------|---------|
| `/search/advanced/profiles` | Search profiles by identity, tags, rank, verification |
| `/profile` | Get profile by ID |
| `/accounts` | Get connected wallets, GitHub, socials |
| `/socials` | Get social profiles + bios |
| `/credentials` | Get data points (earnings, followers, hackathons, etc.) |
| `/scores` | Get ranks (default) or scores (only when explicitly asked) |
| `/human_checkmark` | Check if human-verified (only use when user asks) |
| `/data_points` | List available data point slugs |
| `/data_issuers_meta` | List all data issuers and credential slugs |

## Critical Rules

- **Ranks by default.** Use `builder_score.rank_position`. Only return `points` when the user explicitly asks for scores.
- **Location filtering is broken via GET.** Do NOT use `query[standardized_location]`. Use POST with `customQuery` regex instead — see [use-cases.md](references/use-cases.md#by-location-country).
- **`human_checkmark` is opt-in.** It reduces results significantly. Only add when the user explicitly requests verified humans.
- **URL encoding:** `[` = `%5B`, `]` = `%5D`, Space = `%20`
- **Max 250 results per page.**

## Alternative: Talent Agent CLI

If `talent-agent` is installed (`npm install -g talent-agent`), it provides natural language search without manual API calls. It can also run as an MCP server (`talent-agent --serve`) for use in Cursor, Claude, and other MCP-compatible clients.

## References

- [endpoints.md](references/endpoints.md) — Full endpoint docs with parameters and response schemas
- [use-cases.md](references/use-cases.md) — Common patterns and examples
- [github-enrichment.md](references/github-enrichment.md) — GitHub API enrichment flow
