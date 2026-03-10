# Use Cases

---

## Identity → Wallets

```bash
# 1. Search by identity
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/search/advanced/profiles?query%5Bidentity%5D={handle}&query%5Bidentity_type%5D={identity_type}"

# 2. Disambiguate — pick the profile with the highest builder_score

# 3. Get wallets from profile ID
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/accounts?id={profile_id}"
# Filter: source = "wallet"
```

**Identity types:** `twitter`, `github`, `farcaster`, `ens`, `lens`, `basename`, `wallet`

---

## Top Builders

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/search/advanced/profiles?sort%5Bscore%5D%5Border%5D=desc&sort%5Bscore%5D%5Bscorer%5D=Builder%20Score&per_page=25"
```

---

## By Location (Country)

> **Broken.** Both GET and POST location params fail. Workaround: fetch broadly + filter client-side by `standardized_location` or `location`. Since `per_page` max is 25, you may need to paginate through multiple pages.

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/search/advanced/profiles?sort%5Bscore%5D%5Border%5D=desc&sort%5Bscore%5D%5Bscorer%5D=Builder%20Score&per_page=25&page=1"
# Then filter results where standardized_location/location contains target country
# Repeat with page=2, page=3, etc. until enough matches are found
```

---

## Credentials

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/credentials?id={profile_id}"
```

Discover all available slugs:

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/data_issuers_meta"
```

---

## GitHub Enrichment

See [github-enrichment.md](github-enrichment.md) for full flow.

```bash
# 1. Get GitHub username from /accounts (source = "github")
# 2. Query GitHub API
curl "https://api.github.com/users/{username}"
curl "https://api.github.com/users/{username}/repos?sort=stars&per_page=5"
```

---

## Batch Farcaster Scores

```bash
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/farcaster/scores?fids%5B%5D=123&fids%5B%5D=456"
```
