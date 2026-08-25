# JHEEM Simulations

Organization-owned, versioned simulation and derived model artifacts for JHEEM models, distributed
through immutable GitHub Releases.

This repository was transferred from `ncsizemore/jheem-simulations` to
`CIPHER-Epi/jheem-simulations` on 2026-08-25. The GitHub repository identity, history, tags,
releases, asset IDs, sizes, and digests were preserved. The former URL remains a compatibility
redirect, but new consumers must use the organization-owned namespace.

See the [transfer verification record](docs/REPOSITORY-TRANSFER-2026-08-25.md) and
[release contract](docs/RELEASE-CONTRACT.md).

## Current Releases

| Release | Model | Content | Status |
|---------|-------|---------|--------|
| `ehe-msa-v1.0.0` | EHE MSA calibrated baselines | 31 cities, 93 files, 56.35 GiB | Active |
| `ehe-state-v1.0.0` | EHE state calibrated baselines | 30 states, 55 files, 51.79 GiB | Active |
| `ryan-white-msa-v1.0.0` | Ryan White MSA | 31 cities, 124 files, 3.34 GiB | Active |
| `ryan-white-ajph-v1.0.0` | Ryan White AJPH | 11 states, 44 files, 22.58 GiB | Active |
| `ryan-white-state-v2.0.0` | Ryan White CROI | 30 states, 150 files, 74.09 GiB | Active |
| `cdc-testing-v1.0.0` | CDC Testing | 18 states, 72 files, 85.12 GiB | Active |

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
  ryan-white-state-v2.0.0    # Ryan White state-level (CROI, 30 states)
  cdc-testing-v1.0.0         # CDC Testing (18 states)
```

## File Structure

Each release contains per-location simulation files:

**Ryan White MSA:**
```
{location}_base.Rdata           # Baseline (no intervention)
{location}_cessation.Rdata      # Funding cessation scenario
{location}_brief_interruption.Rdata
{location}_prolonged_interruption.Rdata
```

State-level Ryan White releases retain their source-analysis filenames. Consumers should select
assets using the exact versioned patterns recorded in backend model configuration rather than
renaming release assets.

**EHE calibrated baselines:**
```
ehe_final.ehe-1000_{location}_{scenario}.Rdata
ehe_final.ehe.state-1000_{state}_{scenario}.Rdata
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
      --repo CIPHER-Epi/jheem-simulations \
      --pattern "C.12580_*.Rdata" \
      --dir simulations/
```

### Local download

```bash
# Download specific location
gh release download ryan-white-ajph-v1.0.0 \
  --repo CIPHER-Epi/jheem-simulations \
  --pattern "AL_*.Rdata" \
  --dir ./simulations

# Download entire release (large!)
gh release download cdc-testing-v1.0.0 \
  --repo CIPHER-Epi/jheem-simulations \
  --dir ./simulations
```

## Why GitHub Releases?

| Aspect | GitHub Releases | S3 |
|--------|-----------------|-----|
| Egress cost | Free | ~$0.09/GB |
| Auth in Actions | Built-in `GITHUB_TOKEN` | Requires AWS secrets |
| Network speed | Same datacenter as runners | Cross-service |

For a 75GB release (CROI), this saves ~$7 per workflow run.

## Creating a New Release

1. **Prepare files** with consistent naming (see conventions above) and record their SHA-256
   digests, provenance, citation, and reuse metadata.
2. **Create release** via GitHub UI or CLI:
   ```bash
   gh release create my-model-v1.0.0 \
     --title "My Model v1.0.0" \
     --notes "Description of the release" \
     ./simulations/*.Rdata
   ```
3. **Verify the published assets from a fresh directory** against the approved sizes and SHA-256
   digests.
4. **Update models.json** in jheem-backend with the new release tag.
5. **Create/update workflow** in jheem-backend to use the release.

## Related Repositories

| Repository | Purpose |
|------------|---------|
| [jheem-backend](https://github.com/ncsizemore/jheem-backend) | Workflows that consume these releases |
| [jheem-portal](https://github.com/ncsizemore/jheem-portal) | Frontend that displays extracted data |
| jheem-*-container | R containers that process simulation files |
