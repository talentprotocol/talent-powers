# GitHub Enrichment

Enrich Talent profiles with GitHub data by resolving identity → GitHub username → GitHub API.

**GitHub Token:** https://github.com/settings/tokens — "Generate new token (classic)", no scopes needed for public data.

| API | Without token | With token |
|-----|---------------|------------|
| REST | 60/hr | 5,000/hr |
| Search | 10/min | 30/min |

**Auth header:** `-H "Authorization: token $GITHUB_TOKEN"`

---

## Flow

```bash
# 1. Get GitHub username from Talent Protocol
curl -H "X-API-KEY: $TALENT_API_KEY" \
  "https://api.talentprotocol.com/accounts?id={profile_id}"
# Look for: { "source": "github", "username": "..." }

# 2. Use that username in the GitHub API calls below
```

---

## Endpoints

| Data | Endpoint |
|------|----------|
| Profile & company | `GET /users/{username}` |
| Top repos | `GET /users/{username}/repos?sort=stars&per_page=5` |
| Recent repos | `GET /users/{username}/repos?sort=pushed&per_page=5` |
| Activity/commits | `GET /users/{username}/events/public` |
| Open PRs | `GET /search/issues?q=author:{username}+type:pr+state:open&per_page=5` |
| README | `GET /repos/{owner}/{repo}/readme` |
| Languages | `GET /repos/{owner}/{repo}/languages` |

---

## Profile

```bash
curl https://api.github.com/users/{username}
```

```json
{
  "login": "jessepollak",
  "name": "Jesse Pollak",
  "company": "@coinbase",
  "bio": "Building @base",
  "location": "San Francisco",
  "public_repos": 142,
  "followers": 5200
}
```

---

## Repos

```bash
curl "https://api.github.com/users/{username}/repos?sort=stars&direction=desc&per_page=5"
curl "https://api.github.com/users/{username}/repos?sort=pushed&direction=desc&per_page=5"
```

```json
{
  "name": "repo-name",
  "description": "...",
  "stargazers_count": 1250,
  "language": "TypeScript",
  "fork": false,
  "pushed_at": "2024-01-28T00:00:00Z"
}
```

Filter forked repos with `fork=true`. Aggregate the `language` field across repos for a tech stack summary.

---

## Open PRs

```bash
curl "https://api.github.com/search/issues?q=author:{username}+type:pr+state:open&per_page=5"
```

---

## Activity & Commits

```bash
curl "https://api.github.com/users/{username}/events/public?per_page=100"
```

Filter by `type`: `PushEvent` (commits), `PullRequestEvent` (PRs), `IssuesEvent` (issues).

Extract commits from PushEvent:

```javascript
events.filter(e => e.type === 'PushEvent')
  .flatMap(e => e.payload.commits)
  .slice(0, 5)
```

---

## README

```bash
curl -H "Accept: application/vnd.github.raw" \
  "https://api.github.com/repos/{owner}/{repo}/readme"
```
