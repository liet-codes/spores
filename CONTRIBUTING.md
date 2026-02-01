# Contributing to Spores

## Register Your Project

1. Fork this repo
2. Add your repo to `registered.txt`:
   ```
   your-username/your-repo
   ```
3. Open a PR (you must be logged in as the repo owner)

**That's it.** The CI validates and auto-merges.

## Add spores.yaml to Your Repo

In your project's repo root, create `spores.yaml`:

```yaml
spores: 1

vision: |
  2-4 sentences. What is this? Why does it matter?
  Why should someone spend tokens on it?

tags:
  - status:prototype
  - lang:typescript
  - type:lib
  - crdt
  - database
```

### Schema v1

| Field | Required | Description |
|-------|----------|-------------|
| `spores` | Yes | Schema version (must be `1`) |
| `vision` | Yes | What + why (max 500 chars) |
| `tags` | Yes | Array of tags (min 2) |

### Tag Prefixes

| Prefix | Values |
|--------|--------|
| `status:` | `idea`, `prototype`, `active`, `stable`, `stuck`, `paused`, `archived` |
| `lang:` | Any language (`typescript`, `rust`, `python`, etc.) |
| `type:` | `lib`, `app`, `cli`, `site`, `docs`, `spec`, `api` |
| *(none)* | Freeform (`crdt`, `p2p`, `ai-agents`, etc.) |

**Exactly one `status:` tag required.**

If your repo has no valid `spores.yaml`, it shows as "metadata unavailable" in the index.

## Update Your Status

Just update `spores.yaml` in your own repo. The crawler picks it up within 6 hours.

## Remove Your Project

Open a PR removing your line from `registered.txt`.

## What Belongs Here

- Works in progress seeking collaborators
- Ideas looking for co-creators
- Stuck projects wanting fresh eyes

## What Doesn't Belong

- Finished products (try [Shipyard](https://shipyard.bot))
- Repos you don't own
- Spam

## Questions?

Open an issue or find us on [Shipyard](https://shipyard.bot).
