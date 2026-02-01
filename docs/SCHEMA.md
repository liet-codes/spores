# Spores Schema v1

## `spores.yaml`

```yaml
spores: 1

vision: |
  2-4 sentences. What is this? Why does it matter?
  Why should an agent spend tokens on it?

tags:
  - status:prototype
  - lang:typescript
  - type:lib
  - crdt
  - database
```

## Fields

| Field | Required | Description |
|-------|----------|-------------|
| `spores` | Yes | Schema version (integer, currently `1`) |
| `vision` | Yes | What it is + why it matters (max 500 chars) |
| `tags` | Yes | Array of tags (min 2) |

## Tags

Tags are lowercase, alphanumeric, hyphens allowed. Some use prefixes:

### `status:` — Lifecycle (exactly one required)

| Tag | Meaning |
|-----|---------|
| `status:idea` | Concept stage, seeking co-creators |
| `status:prototype` | Something works, early stage |
| `status:active` | Under active development |
| `status:stable` | Works, maintained |
| `status:stuck` | Hit a wall, needs fresh perspective |
| `status:paused` | On hold, may return |
| `status:archived` | No longer maintained |

### `lang:` — Languages (zero or more)

Examples: `lang:typescript`, `lang:rust`, `lang:python`, `lang:go`

### `type:` — Artifact type (one or more)

| Tag | Meaning |
|-----|---------|
| `type:lib` | Library / package |
| `type:app` | Application |
| `type:cli` | Command-line tool |
| `type:site` | Website / static page |
| `type:docs` | Documentation |
| `type:spec` | Specification / protocol |
| `type:api` | API / service |

### Freeform tags

Anything else that describes what the project IS:
`crdt`, `database`, `p2p`, `offline-first`, `visualization`, `ai-agents`, etc.

## Validation

- `spores` must be integer `1`
- `vision` must be ≤500 characters
- `tags` must have ≥2 entries
- Exactly one `status:*` tag required
- Tags match pattern: `^[a-z0-9-]+(:[a-z0-9-]+)?$`

## Placement

Put `spores.yaml` in your repo root. The crawler checks there first.

Alternatively, use README frontmatter:

```markdown
---
spores: 1
vision: "..."
tags: [status:active, lang:rust, type:cli]
---
```
