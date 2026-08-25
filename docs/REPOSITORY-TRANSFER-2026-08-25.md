# Repository transfer verification — 2026-08-25

## Scope

The existing public repository was transferred server-side from
`ncsizemore/jheem-simulations` to `CIPHER-Epi/jheem-simulations`. No release was copied,
repacked, renamed, or republished as part of the transfer.

## Identity and Git verification

| Field | Verified value |
|---|---|
| GitHub repository ID | `1129225632` |
| GitHub node ID | `R_kgDOQ06doA` |
| Default branch | `main` |
| `main` commit | `3ef5c66b013aff3657bbcb9c2ad54b97d0a5814d` |
| Releases | 8 |
| Release assets | 732 |
| Total release bytes | 326,692,097,921 |

The repository ID, node ID, Git refs, release IDs, asset IDs, filenames, sizes, and GitHub-provided
SHA-256 digests matched before and after transfer. Git operations through the former and current
namespaces resolved to the same `HEAD`, `main`, and representative tag refs.

## Representative release evidence

| Release | Release ID | Assets | Bytes | Representative asset SHA-256 |
|---|---:|---:|---:|---|
| `ehe-state-v1.0.0` | 339619455 | 55 | 55,611,249,753 | `5b106470ab9280f62cbcee6b60cb8815c6d3a1165ec0b964a135d02616ece355` |
| `ehe-msa-v1.0.0` | 324389220 | 93 | 60,500,648,126 | `432bd5a9075ed7b059db303be8dbaea051000703bc738a10a3065c331b6ee7bb` |
| `ryan-white-state-v2.0.0-web` | 279427047 | 150 | 8,927,910,590 | `c48a735ba27a4d95006c653be61e5a99f3cd6f1896e680b64b3279faac46a805` |
| `ryan-white-state-v2.0.0` | 277196996 | 150 | 79,555,225,943 | `54209bf88f690b49921306de5a3ed5c92aa6069726c89e6c08686ab8cf861edd` |
| `ryan-white-state-v1.0.0` | 274864042 | 44 | 2,869,292,856 | `d91494dcc04432128030e9aa055cab0993f0505a51a58bd08bd684b12d4e4822` |
| `ryan-white-msa-v1.0.0` | 282997550 | 124 | 3,584,881,372 | `989b9f1366757d4a8cdba060d031849179701cccf56467dd9b65a6a15cff67e8` |
| `ryan-white-ajph-v1.0.0` | 283084390 | 44 | 24,247,677,354 | `54209bf88f690b49921306de5a3ed5c92aa6069726c89e6c08686ab8cf861edd` |
| `cdc-testing-v1.0.0` | 283095538 | 72 | 91,395,211,927 | `9b800b21a9f5e526b9b0c390c42fd932a626d87584b02407f446009ee6ac423e` |

All 732 assets exposed a GitHub-provided SHA-256 digest at verification time. The representative
digest is the lexicographically first asset in each release and is a transfer sentinel, not a
substitute for the full per-asset digest inventory retained by GitHub.

## Compatibility boundary

GitHub redirects the former repository namespace to the organization-owned repository. Consumers
must nevertheless use `CIPHER-Epi/jheem-simulations` explicitly so provenance does not depend on
redirect behavior or continued availability of a personal namespace.
