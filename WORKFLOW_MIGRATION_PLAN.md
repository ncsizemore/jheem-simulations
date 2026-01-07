# GitHub Releases Simulation Hosting - Migration Plan

## Overview

Migrate simulation data from S3 to GitHub Releases to eliminate data transfer costs during GitHub Actions workflows.

**Repo:** https://github.com/ncsizemore/jheem-simulations

## Architecture Decisions

### Single Container for Ryan White (MSA + State-Level)
- Same container (`ghcr.io/ncsizemore/jheem-ryan-white-model`) serves both MSA and state-level
- Container treats location generically - works with MSA codes (C.12580) or state codes (AL)
- Simulations mounted to `simulations/ryan-white/` regardless of model variant
- No separate state-level container needed

### jheem_analyses Versioning
- Container pinned to `fc3fe1d2` (Jul 2025) - **do not upgrade for Ryan White**
- Papers published, simulations static → stability over new features
- Future models can use appropriate commits for their simulation generation

### Container Registry Strategy
- **ghcr.io** → GitHub Actions workflows (free, fast)
- **ECR** → AWS Lambda/Fargate (free within AWS)
- **Docker Hub** → Public availability

## Release Naming Strategy

```
{model}-{version}

Examples:
  ryan-white-v1.0.0           # Ryan White MSA-level
  ryan-white-state-v1.0.0     # Ryan White state-level
  ehe-v1.0.0                  # Future EHE model
```

## Release Asset Structure

Each release contains per-location files (individual files, no tarballs):
```
AL_base.Rdata
AL_cessation.Rdata
AL_brief_interruption.Rdata
AL_prolonged_interruption.Rdata
CA_base.Rdata
...
```

## Workflow Changes

### State-Level Workflow (generate-native-data-ryan-white-state.yml)

Downloads from GitHub Releases, mounts to container's expected path:
```yaml
- name: Download simulation data from GitHub Release
  env:
    GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  run: |
    # Mount to simulations/ryan-white/ to match container's expected path
    mkdir -p simulations/ryan-white/base
    mkdir -p simulations/ryan-white/prerun/${{ matrix.state }}

    gh release download ${{ inputs.simulation_release }} \
      --repo ncsizemore/jheem-simulations \
      --pattern "${{ matrix.state }}_*.Rdata" \
      --dir ./tmp-download

    # Move files to expected structure
    mv ./tmp-download/${{ matrix.state }}_base.Rdata \
      simulations/ryan-white/base/${{ matrix.state }}_base.Rdata
    mv ./tmp-download/${{ matrix.state }}_cessation.Rdata \
      simulations/ryan-white/prerun/${{ matrix.state }}/cessation.Rdata
    # ... etc
```

### MSA-Level Workflow (future migration)

Same pattern - download from GitHub Releases instead of S3.

## Cost Comparison

| Approach | Per-Run Cost | Monthly Storage |
|----------|--------------|-----------------|
| S3 | ~$1.03 (11.4GB egress) | ~$0.26 |
| GitHub Releases | $0 | $0 (public repo) |
| ECR (container pulls) | ~$7 | - |
| ghcr.io (container pulls) | $0 | $0 |

## Migration Checklist

### Completed
- [x] Create jheem-simulations repo
- [x] Upload ryan-white-state-v1.0.0 release (11 states, 44 files)
- [x] Create state-level workflow (generate-native-data-ryan-white-state.yml)
- [x] Add ghcr.io to container build pipeline
- [x] Update MSA workflow to pull container from ghcr.io (instead of ECR)
- [x] Verify single container works for both MSA and state-level

### Pending
- [ ] Test state-level workflow end-to-end
- [ ] Verify aggregation scripts work with state codes
- [ ] Upload ryan-white-v1.0.0 release (MSA-level, ~$1 S3 download cost)
- [ ] Update MSA workflow to use GitHub Releases for simulations

### Known Issues to Verify When Testing
1. Container CLI: `--city` flag with state codes (AL, CA) - should work but verify
2. Aggregation script (`aggregate-city-data.ts`) - may need updates for state codes
3. Summary script (`generate-city-summaries.ts`) - may need state-aware version

## Future Considerations

### Parameterize Simulation Path (Optional Enhancement)
Currently hardcoded to `simulations/ryan-white/`. Could add `--simulation-root` arg for cleaner multi-model support. Low priority since mounting to expected path works.

### Monorepo vs Separate Repos
Current decision: **Separate repos per model** (Option A)
- Freeze benefit for published models
- Independence during development
- Reassess at 5+ models if repo sprawl becomes painful
