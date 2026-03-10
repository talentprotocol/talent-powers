# Endpoints

All endpoints use `GET`. Auth: `-H "X-API-KEY: $TALENT_API_KEY"`

URL encoding: `[` = `%5B`, `]` = `%5D`

---

## /search/advanced/profiles

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/search/advanced/profiles?query%5Bidentity%5D=jessepollak&query%5Bidentity_type%5D=twitter"
```

| Parameter | Example |
|-----------|---------|
| `query[identity]` | `jessepollak` |
| `query[identity_type]` | `twitter`, `github`, `farcaster`, `ens`, `wallet` |
| `query[verified_nationality]` | `true` |
| `query[tags][]` | `developer` |
| `sort[score][order]` | `desc` |
| `sort[score][scorer]` | `Builder Score` |
| `page` | `1` |
| `per_page` | `25` (max; API rejects values > 25) |

> **Location filtering is broken.** Both GET `query[standardized_location]` and POST `customQuery` regex return no results (POST gives 302). Filter client-side instead.

**Response:**

```json
{
  "profiles": [{
    "id": "uuid",
    "name": "username",
    "display_name": "Name",
    "bio": "...",
    "location": "east bay & the internet",
    "standardized_location": "Unknown",
    "human_checkmark": true,
    "tags": ["developer"],
    "builder_score": { "rank_position": 4967, "points": 17, "slug": "builder_score" },
    "scores": [
      { "slug": "builder_score", "rank_position": 4967, "points": 17 },
      { "slug": "builder_score_2025", "rank_position": 244, "points": 213 },
      { "slug": "creator_score", "rank_position": null, "points": 138 }
    ],
    "verified_nationality": true,
    "talent_protocol_id": 353682
  }],
  "pagination": { "current_page": 1, "last_page": 2, "total": 100, "total_for_page": 25 }
}
```

---

## /profile

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/profile?id={profile_id}"
```

| Parameter | Description |
|-----------|-------------|
| `id` | Profile ID, wallet, or identifier |
| `account_source` | `farcaster`, `github`, `wallet` |

---

## /accounts

Get connected wallets and platforms. Use to find GitHub username for enrichment.

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/accounts?id={profile_id}"
```

**Response:**

```json
{
  "accounts": [
    { "source": "wallet", "identifier": "0x...", "username": "0x..." },
    { "source": "github", "username": "jessepollak" },
    { "source": "farcaster", "username": "jesse.base.eth" },
    { "source": "x_twitter", "username": "jessepollak" },
    { "source": "linkedin", "username": "jessepollak" },
    { "source": "worldcoin", "username": null },
    { "source": "talent_protocol", "username": "jessepollak" }
  ]
}
```

---

## /socials

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/socials?id={profile_id}"
```

**Response:**

```json
{
  "socials": [
    { "social_name": "Twitter", "social_slug": "twitter", "handle": "jessepollak", "bio": "...", "followers_count": 236929, "profile_url": "https://www.x.com/jessepollak" },
    { "social_name": "Farcaster", "social_slug": "farcaster", "handle": "jesse.base.eth", "followers_count": 377811 },
    { "social_name": "Github", "social_slug": "github", "handle": "jessepollak", "followers_count": 1306, "location": "Oakland, CA" }
  ]
}
```

Other social types: Zora, Basenames, EFP, Linkedin

---

## /scores

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/scores?id={profile_id}"
```

**Response:**

```json
{
  "scores": [
    { "slug": "builder_score", "rank_position": 4967, "points": 17 },
    { "slug": "builder_score_2025", "rank_position": 244, "points": 213 },
    { "slug": "creator_score", "rank_position": null, "points": 138 }
  ]
}
```

---

## /credentials

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/credentials?id={profile_id}"
```

**Response:**

```json
{
  "credentials": [
    { "slug": "github_total_contributions", "name": "GitHub Contributions", "category": "Activity", "readable_value": "74", "uom": "contributions", "points": 11, "max_score": 12 },
    { "slug": "total_builder_earnings", "name": "Builder Earnings", "category": "Impact", "readable_value": "5.51", "uom": "USDC", "points": 9 },
    { "slug": "talent_protocol_verified_builder", "name": "Verified Onchain Builder", "category": "Badge", "points": 8 },
    { "slug": "base_basecamp", "name": "Basecamp Attendee", "category": "Badge", "points": 4 }
  ]
}
```

**Common slugs:**

| Category | Slugs |
|----------|-------|
| Earnings | `total_earnings`, `total_builder_earnings`, `base_builder_rewards_eth` |
| Events | `base_basecamp`, `farcaster_farcon_nyc_2025_attendee` |
| Hackathons | `eth_global_hacker`, `eth_global_finalist`, `devfolio_hackathons_won` |
| Verification | `talent_protocol_human_checkmark`, `world_id_human`, `coinbase_verified_account` |

---

## /human_checkmark

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/human_checkmark?id={profile_id}"
```

**Response:** `{ "humanity_verified": true }`

---

## /data_points

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/data_points?id={profile_id}"
```

---

## /data_issuers_meta

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/data_issuers_meta"
```

---

## Errors

| Status | Meaning |
|--------|---------|
| 400 | Bad request (e.g., per_page > 25) |
| 401 | Missing/invalid API key |
| 404 | Profile not found |
| 302 | Redirect — POST endpoint broken or deprecated |
