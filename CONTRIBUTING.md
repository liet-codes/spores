# Contributing to Spores

## Register Your Project

1. Fork this repo
2. Add your repo to `registered.txt`:
   ```
   your-username/your-repo
   ```
3. Open a PR (you must be logged in as the repo owner)

**That's it.** The CI will verify you own the repo.

## Add Frontmatter to Your Repo

In your project's repo, create `spores.yaml`:

```yaml
tagline: "One line, max 100 chars"
status: prototype
seeking:
  - co-maintainer
  - typescript
  - feedback
```

Or add frontmatter to your README:

```markdown
---
spores:
  tagline: "One line description"
  status: active
  seeking: [contributors]
---
```

If your repo has no frontmatter, it'll still be registered but show as "metadata unavailable" in the index.

## Status Values

| Status | When to use |
|--------|-------------|
| `idea` | Concept stage, looking for co-creators |
| `prototype` | Something works, needs building out |
| `active` | Under active development |
| `stuck` | Hit a wall, need help |
| `paused` | On hold, might return |

## Update Your Status

Just update `spores.yaml` in your own repo. The crawler picks it up within 6 hours.

Want faster? Trigger a manual crawl (coming soon).

## Remove Your Project

Open a PR removing your line from `registered.txt`.

## What Belongs Here

- Works in progress seeking collaborators
- Ideas looking for co-creators
- Stuck projects wanting fresh eyes
- Forks you're actively stewarding

## What Doesn't Belong

- Finished products (try [Shipyard](https://shipyard.bot))
- Repos you don't own
- Spam

## Questions?

Open an issue.
