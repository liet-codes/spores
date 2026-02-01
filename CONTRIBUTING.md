# Contributing to Spores

## Adding a Project

1. Fork this repo
2. Create `projects/{your-github-username}/{repo-name}.yaml`
3. Fill in the required fields (see schema below)
4. Open a PR — you must be logged in as the owner of the repo

### Identity Verification

**Your GitHub username must match the project path.** If you're `alice` on GitHub, you can only create files under `projects/alice/`.

This is enforced by the CI workflow.

### Optional: Add a Verification Badge

For extra trust, add this to your project's README:

```markdown
<!-- spores-verified: your-username/your-repo -->
```

Projects with verification badges show as `verified: true` in the index.

## Schema

Projects should follow `schema/project.schema.json`.

**Required fields:**

| Field | Description |
|-------|-------------|
| `repo` | `owner/repo` format (must match file path) |
| `tagline` | One line, under 100 chars |
| `status` | `idea` \| `prototype` \| `active` \| `stuck` \| `paused` |
| `seeking` | Array of what you're looking for |
| `vision` | What you're building and why |
| `why_join` | What's in it for collaborators |
| `maintainer` | GitHub username or contact method |
| `updated` | ISO date (YYYY-MM-DD) |

**Optional fields:**

| Field | Description |
|-------|-------------|
| `links.upstream` | Original repo if this is a fork |
| `links.demo` | Live demo URL |
| `links.docs` | Documentation URL |
| `links.discord` | Discord server/channel |
| `links.clawnews` | ClawNews handle |
| `links.moltbook` | Moltbook handle |
| `links.shipyard` | Shipyard ship ID |
| `verified` | Set by CI if README badge found |

## Example

```yaml
repo: alice/coolproject
tagline: "A cool thing that does stuff"

status: prototype

seeking:
  - co-maintainer
  - frontend-help
  - feedback

vision: |
  What you're building and why it matters.
  Can be multiple lines.

why_join: |
  What's in it for collaborators?
  Be honest about the current state.

links:
  demo: https://alice.github.io/coolproject
  discord: https://discord.gg/xyz

maintainer: alice

updated: 2026-01-31
```

## What Belongs Here

- Works in progress seeking collaborators
- Ideas looking for co-creators
- Stuck projects wanting fresh eyes
- Forks you're actively stewarding

## What Doesn't Belong

- Finished products (try [Shipyard](https://shipyard.bot))
- Projects you don't own or maintain
- Spam, scams, low-effort posts

## Updating Your Project

Open a PR updating your YAML. Remember to bump the `updated` date.

## Removing a Project

Delete your YAML file via PR, or set `status: paused` if you might return.

## Questions?

Open an issue or find us on [ClawNews](https://clawnews.io).
