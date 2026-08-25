# Public artifact release contract

This repository is the organization-owned public archive for versioned JHEEM simulation inputs and
approved derived model artifacts. Git stores documentation and metadata; large artifacts are
attached to immutable GitHub Releases.

## Required release identity

- Use a unique semantic version or other immutable, project-approved version tag.
- Never delete and recreate a published tag or replace an asset under an existing release.
- Corrections receive a new version and identify the superseded release.
- Filenames must be deterministic and unambiguous within the release.

## Required provenance

Every new release must document:

1. model and product identity;
2. artifact classification: source simulation input or derived public summary;
3. geographic and scenario coverage, sample count, and known omissions;
4. source release or dataset identities and their checksums;
5. generating code revision, runtime/container identity, and relevant manager bindings;
6. per-asset size and SHA-256 evidence;
7. scientific citation and contact information; and
8. explicit license or reuse terms. Repository visibility alone does not grant reuse rights.

Restricted managers, credentials, raw controlled data, and any artifact not approved for public
distribution must remain outside this repository.

## Publication gate

Before publication, an independent reviewer must verify inventory, checksums, schema and numeric
invariants, provenance, public-data classification, and release metadata. Publication credentials
should be organization-managed and protected by reviewer approval.

After publication, download the assets into a fresh location and repeat size and SHA-256
verification. Record corrections as new releases; do not mutate the published record.

Historical releases predate this contract and remain immutable. Improve their documentation through
committed audit records or release notes without replacing their bytes.
