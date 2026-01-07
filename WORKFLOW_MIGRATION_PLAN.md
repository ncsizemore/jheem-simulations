# GitHub Releases Simulation Hosting - Migration Plan

## Overview

Migrate simulation data from S3 to GitHub Releases to eliminate data transfer costs during GitHub Actions workflows.

**Repo:** https://github.com/ncsizemore/jheem-simulations

## Release Naming Strategy

```
{model}-{version}

Examples:
  ryan-white-v1.0.0           # Ryan White MSA-level
  ryan-white-state-v1.0.0     # Ryan White state-level
  ehe-v1.0.0                  # Future EHE model
```

## Release Asset Structure

Each release contains per-location files (no tarballs, individual files for flexibility):
```
AL_base.Rdata
AL_cessation.Rdata
AL_brief_interruption.Rdata
AL_prolonged_interruption.Rdata
CA_base.Rdata
...
```

## Workflow Changes (generate-native-data.yml)

### 1. Add simulation release input

```yaml
inputs:
  simulation_release:
    description: 'Simulation release tag (e.g., ryan-white-state-v1.0.0)'
    required: true
    type: string
```

### 2. Replace S3 download with GitHub Release download

```yaml
- name: Download simulation data
  env:
    GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  run: |
    echo "📥 Downloading simulations from release ${{ inputs.simulation_release }}"

    mkdir -p simulations/base
    mkdir -p simulations/prerun/${{ matrix.location }}

    # Download all files for this location
    gh release download ${{ inputs.simulation_release }} \
      --repo ncsizemore/jheem-simulations \
      --pattern "${{ matrix.location }}_*.Rdata" \
      --dir ./tmp-download

    # Move base file
    mv ./tmp-download/${{ matrix.location }}_base.Rdata simulations/base/

    # Move prerun files
    mv ./tmp-download/${{ matrix.location }}_cessation.Rdata simulations/prerun/${{ matrix.location }}/
    mv ./tmp-download/${{ matrix.location }}_brief_interruption.Rdata simulations/prerun/${{ matrix.location }}/
    mv ./tmp-download/${{ matrix.location }}_prolonged_interruption.Rdata simulations/prerun/${{ matrix.location }}/

    echo "✅ Downloaded simulation data"
```

### 3. AWS credentials

Still needed for:
- Uploading results to S3 (portal data served via CloudFront)

No longer needed for:
- Downloading simulation input data

## Cost Comparison

| Approach | Per-Run Cost | Monthly Storage |
|----------|--------------|-----------------|
| S3 | ~$1.03 (11.4GB egress) | ~$0.26 |
| GitHub Releases | $0 | $0 (public repo) |

## Migration Checklist

- [x] Create jheem-simulations repo
- [x] Upload ryan-white-state-v1.0.0 release
- [ ] Create generate-native-data workflow for state-level model
- [ ] Test workflow with state-level model
- [ ] Upload ryan-white-v1.0.0 release (MSA-level, when ready)
- [ ] Update existing MSA workflow to use GitHub Releases
