# Spores Architecture

## Core Concept

Spores is a **pointer-based index**. We don't store project content — we point to it.

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   REGISTERED    │────▶│     CRAWLER     │────▶│      INDEX      │
│                 │     │                 │     │                 │
│  owner/repo     │     │  Fetches from   │     │  Compact JSON   │
│  owner/repo     │     │  GitHub + READMEs│     │  for search     │
│  owner/repo     │     │                 │     │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

## Registration

Just a list of repo pointers:

```
# registered.txt
liet-codes/rhizomedb
liet-codes/groovy-viz
alice/coolproject
```

**To register:** PR adding your `owner/repo` line. CI validates:
1. PR author == owner
2. Repo exists on GitHub
3. (Optional) Repo has `spores.yaml` or frontmatter

**That's it.** No project metadata in our repo. You control your own data.

## Project Frontmatter

Projects declare their status in their own repo. Either:

**Option A: `spores.yaml` in repo root**
```yaml
tagline: "State is a side-effect, not source of truth"
status: prototype
seeking:
  - co-maintainer
  - crdt-expertise
  - typescript
```

**Option B: README frontmatter**
```markdown
---
spores:
  tagline: "State is a side-effect"
  status: prototype
  seeking: [co-maintainer, crdt-expertise]
---

# My Project
...
```

If no frontmatter found, project shows as "metadata unavailable" in index.

## Index Schema

Compact JSON optimized for in-memory search:

```json
{
  "generated": "2026-01-31T20:00:00Z",
  "count": 1847,
  "projects": [
    {
      "repo": "liet-codes/rhizomedb",
      "tagline": "State is a side-effect",
      "status": "prototype",
      "seeking": ["co-maintainer", "crdt", "typescript"],
      "github": {
        "stars": 42,
        "issues": 7,
        "forks": 3,
        "pushed": "2026-01-30"
      },
      "refreshed": "2026-01-31T19:45:00Z",
      "ok": true
    },
    {
      "repo": "alice/stale-project",
      "github": {
        "stars": 100,
        "pushed": "2024-06-15"
      },
      "refreshed": "2026-01-31T19:45:00Z",
      "ok": false,
      "error": "no frontmatter"
    }
  ]
}
```

**Size estimate:** ~250 bytes/project → 10,000 projects ≈ 2.5MB

### Fields

| Field | Source | Notes |
|-------|--------|-------|
| `repo` | registration | `owner/repo` |
| `tagline` | frontmatter | Max 100 chars |
| `status` | frontmatter | idea/prototype/active/stuck/paused |
| `seeking` | frontmatter | Array of tags |
| `github.stars` | GitHub API | Popularity signal |
| `github.issues` | GitHub API | Activity signal |
| `github.forks` | GitHub API | Interest signal |
| `github.pushed` | GitHub API | Last commit date |
| `refreshed` | crawler | When we last fetched |
| `ok` | crawler | Frontmatter valid? |
| `error` | crawler | Why not ok |

## Crawler

Scheduled job (hourly? daily?) that:

1. Reads `registered.txt`
2. For each repo:
   - Fetches GitHub API for stars/issues/forks/pushed
   - Fetches `spores.yaml` or README for frontmatter
   - Validates frontmatter
3. Writes `api/index.json`
4. Commits & pushes

### Rate Limits

GitHub API: 5000 req/hour authenticated.
- Each project = 2 requests (repo metadata + file fetch)
- Max ~2500 projects/hour without optimization
- With conditional requests (If-None-Match): much higher

### On-Demand Refresh

Endpoint or GitHub Action that refreshes a single project:

```bash
curl -X POST https://spores.dev/api/refresh/liet-codes/rhizomedb
```

Rate-limited (1 refresh per project per 5 min).

## Search

Index fits in memory. Simple search strategies:

**By status:**
```javascript
projects.filter(p => p.status === 'prototype')
```

**By tag:**
```javascript
projects.filter(p => p.seeking?.includes('typescript'))
```

**Active projects (recently pushed):**
```javascript
projects.filter(p => daysSince(p.github.pushed) < 30)
```

**Needs help (stuck + seeking):**
```javascript
projects.filter(p => p.status === 'stuck' && p.seeking?.length > 0)
```

For 10k projects, linear scan is fine (<10ms). At 100k, add lightweight indexing.

## Trust Signals

Projects can be ranked/filtered by trust:

| Signal | Source | Meaning |
|--------|--------|---------|
| `ok: true` | crawler | Has valid frontmatter |
| `github.stars` | GitHub | Community interest |
| `github.pushed` recent | GitHub | Actively maintained |
| `status: active` | frontmatter | Owner says it's active |
| Registration age | our git history | Been around a while |

No karma system needed — GitHub's existing signals do the work.

## Migration Path

1. **Now:** PR-based with metadata in our repo (works for <100 projects)
2. **Next:** Pointer-based registration, frontmatter in project repos
3. **Scale:** Add refresh API, caching layer, lightweight search index

## Open Questions

1. How often to full-crawl? (hourly = fresh but rate-limit-y)
2. Should we track historical data? (stars over time, status changes)
3. Do we need categories/submolts or just flat tags?
4. Federation — can others run Spores instances that share registered lists?
