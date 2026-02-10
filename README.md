# JHEEM Simulations

Simulation data for JHEEM models, distributed via GitHub Releases for fast, free downloads in GitHub Actions workflows.

## Current Releases

| Release | Model | Content | Status |
|---------|-------|---------|--------|
| `ryan-white-msa-v1.0.0` | Ryan White MSA | 31 cities, 124 files, 3.3GB | Active |
| `ryan-white-ajph-v1.0.0` | Ryan White AJPH | 11 states, 44 files, 23GB | Active |
| `ryan-white-state-v2.0.0` | Ryan White CROI | 30 states, 120 files, 75GB | Active |
| `cdc-testing-v1.0.0` | CDC Testing | 18 states, 72 files, 86GB | Active |

**Deprecated releases:**
| Release | Notes |
|---------|-------|
| `ryan-white-state-v1.0.0` | Superseded by `ryan-white-ajph-v1.0.0` (wrong simulation version) |
| `ryan-white-state-v2.0.0-web` | Trimmed backup, not needed (direct approach works) |

## Release Naming Convention

```
{project}-{model}-v{semver}

Examples:
  ryan-white-msa-v1.0.0      # Ryan White city-level (31 MSAs)
  ryan-white-ajph-v1.0.0     # Ryan White state-level (AJPH paper, 11 states)
  ryan-white-croi-v1.0.0     # Ryan White state-level (CROI, 30 states) - future
  cdc-testing-v1.0.0         # CDC Testing (18 states)
```

## File Structure

Each release contains per-location simulation files:

**Ryan White models:**
```
{location}_base.Rdata           # Baseline (no intervention)
{location}_cessation.Rdata      # Funding cessation scenario
{location}_brief_interruption.Rdata
{location}_prolonged_interruption.Rdata
```

**CDC Testing:**
```
cdct_final.ehe.state-1000_{state}_noint.Rdata    # Baseline
cdct_final.ehe.state-1000_{state}_cdct.end.Rdata # Cessation
cdct_final.ehe.state-1000_{state}_cdct.bintr.Rdata
cdct_final.ehe.state-1000_{state}_cdct.pintr.Rdata
```

## Usage

### In GitHub Actions (recommended)

```yaml
- name: Download simulation data
  env:
    GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  run: |
    gh release download ryan-white-msa-v1.0.0 \
      --repo ncsizemore/jheem-simulations \
      --pattern "C.12580_*.Rdata" \
      --dir simulations/
```

### Local download

```bash
# Download specific location
gh release download ryan-white-ajph-v1.0.0 \
  --pattern "AL_*.Rdata" \
  --dir ./simulations

# Download entire release (large!)
gh release download cdc-testing-v1.0.0 --dir ./simulations
```

## Why GitHub Releases?

| Aspect | GitHub Releases | S3 |
|--------|-----------------|-----|
| Egress cost | Free | ~$0.09/GB |
| Auth in Actions | Built-in `GITHUB_TOKEN` | Requires AWS secrets |
| Network speed | Same datacenter as runners | Cross-service |

For a 75GB release (CROI), this saves ~$7 per workflow run.

## Creating a New Release

1. **Prepare files** with consistent naming (see conventions above)
2. **Create release** via GitHub UI or CLI:
   ```bash
   gh release create my-model-v1.0.0 \
     --title "My Model v1.0.0" \
     --notes "Description of the release" \
     ./simulations/*.Rdata
   ```
3. **Update models.json** in jheem-backend with the new release tag
4. **Create/update workflow** in jheem-backend to use the release

## Related Repositories

| Repository | Purpose |
|------------|---------|
| [jheem-backend](https://github.com/ncsizemore/jheem-backend) | Workflows that consume these releases |
| [jheem-portal](https://github.com/ncsizemore/jheem-portal) | Frontend that displays extracted data |
| jheem-*-container | R containers that process simulation files |
