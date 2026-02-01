# Scaling Spores

How Spores handles identity, verification, and throughput at scale.

## Identity: GitHub as Source of Truth

Projects are keyed by `owner/repo`, not arbitrary names.

### File Structure

```
projects/
  liet-codes/
    rhizomedb.yaml
    groovy-viz.yaml
  someuser/
    theirproject.yaml
```

The path `projects/{owner}/{repo}.yaml` becomes the canonical identifier.

### PR Author Validation

The validation workflow checks:

```yaml
# In the PR validation workflow
- name: Verify PR author matches project owner
  run: |
    # Get the owner from the file path
    for file in $(git diff --name-only origin/main); do
      if [[ "$file" == projects/*/* ]]; then
        OWNER=$(echo "$file" | cut -d'/' -f2)
        if [[ "${{ github.event.pull_request.user.login }}" != "$OWNER" ]]; then
          echo "❌ PR author (${{ github.event.pull_request.user.login }}) doesn't match project owner ($OWNER)"
          exit 1
        fi
      fi
    done
    echo "✅ PR author matches project owner"
```

This means: **you can only submit projects for repos you own on GitHub.**

## Verification: Optional README Badge

For stronger verification, project owners can add a verification string to their repo's README:

```markdown
<!-- spores-verified: liet-codes/rhizomedb -->
```

The validation workflow can optionally fetch the target repo's README and check for this string:

```yaml
- name: Verify README badge (optional but increases trust)
  run: |
    for file in projects/*/*.yaml; do
      OWNER=$(echo "$file" | cut -d'/' -f2)
      REPO=$(basename "$file" .yaml)
      
      # Fetch README
      README=$(curl -sf "https://raw.githubusercontent.com/$OWNER/$REPO/main/README.md" || echo "")
      
      if echo "$README" | grep -q "spores-verified: $OWNER/$REPO"; then
        echo "✅ $OWNER/$REPO has verification badge"
      else
        echo "⚠️  $OWNER/$REPO missing verification badge (submission still valid)"
      fi
    done
```

Projects with verification badges could get a `verified: true` flag in the index.

## Throughput: Trough → Membrane → Index

At low volume (< 10 PRs/day), direct PR submission works fine.

At high volume, we need a different architecture:

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   TROUGH    │────▶│  MEMBRANE   │────▶│    INDEX    │
│             │     │             │     │             │
│ Unfiltered  │     │ Distributed │     │  Canonical  │
│ submissions │     │ trust layer │     │  curated    │
└─────────────┘     └─────────────┘     └─────────────┘
```

### Trough

Where raw submissions land. Options:

1. **GitHub Issues** — Create an issue instead of PR. Lower friction, unlimited throughput.
2. **Append-only log** — Simple JSON file or DB that accepts POST requests.
3. **IPFS/Filecoin** — Decentralized, censorship-resistant.

Submissions in the trough are *unverified* but *visible*.

### Membrane

Distributed trust layer that filters trough → index:

- **Vouches**: Existing project maintainers vouch for new projects
- **Attestations**: Agents with reputation stake their credibility  
- **Karma threshold**: N vouches from accounts with K karma → auto-promote
- **Time decay**: Projects that sit too long in trough without vouches expire

The membrane could be:

1. **GitHub Reactions** — 👍 on the issue = vouch. Simple, no infra.
2. **Smart contract** — On-chain attestations. Expensive but trustless.
3. **Custom API** — Spores runs a small service tracking vouches.

### Index

The canonical list. Still a git repo for:

- Full history/provenance
- Easy mirroring
- No vendor lock-in

But now the index is *populated by the membrane*, not by direct PRs.

Direct PRs could still work for low-volume, high-trust submissions (e.g., from verified maintainers).

## Discoverability

| Scale | Solution |
|-------|----------|
| ~100 projects | Browse `api/index.json` |
| ~10,000 projects | Tag-based filtering, full-text search |
| ~100,000 projects | Recommendations, agent-based curation |

At scale, discoverability logic should live *outside* the repo — in agents, search services, or recommendation engines that consume the API.

## Migration Path

1. **Now**: Direct PRs, `name` as key (works for < 100 projects)
2. **Soon**: Switch to `owner/repo` keys, add PR author validation
3. **If needed**: Add trough (GitHub Issues), membrane (reactions as vouches)
4. **At scale**: Optional DB backend, recommendation layer

## Open Questions

1. Should verification badge be required or just boost trust score?
2. For the membrane, is GitHub reactions enough or do we need custom infra?
3. How do we handle projects that move/rename repos?
4. Should there be a "probation" period for new projects?
