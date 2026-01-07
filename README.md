# JHEEM Simulations

Simulation data releases for JHEEM models. Data is distributed via GitHub Releases to enable free, fast downloads in CI/CD workflows.

## Available Releases

| Release | Model | Description |
|---------|-------|-------------|
| `ryan-white-state-v1.0.0` | Ryan White State-Level | 11 states |
| `ryan-white-v1.0.0` | Ryan White MSA-Level | 31 cities (planned) |

## Usage

### Download in GitHub Actions

```yaml
- name: Download simulation data
  env:
    GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  run: |
    gh release download ryan-white-state-v1.0.0 \
      --repo ncsizemore/jheem-simulations \
      --pattern "AL_*.Rdata" \
      --dir simulations/
```

### Download locally

```bash
gh release download ryan-white-state-v1.0.0 --pattern "AL_*.Rdata"
```

## Release Structure

Each release contains simulation files per location:

```
{location}_base.Rdata              # Base simulation
{location}_cessation.Rdata         # Prerun: cessation scenario
{location}_brief_interruption.Rdata    # Prerun: brief interruption
{location}_prolonged_interruption.Rdata # Prerun: prolonged interruption
```

Where `{location}` is:
- State code (e.g., `AL`, `CA`) for state-level models
- MSA code (e.g., `C.12060`) for MSA-level models

## Why GitHub Releases?

- **Free egress**: No data transfer costs (vs ~$0.09/GB from S3)
- **Fast in Actions**: Same network as GitHub-hosted runners
- **Simple auth**: Uses `GITHUB_TOKEN`, no extra secrets needed
