# renovate-config

Shared Renovate preset for the anolishq org. The preset is
[`default.json`](default.json).

## Use it

Extend the preset from the repo's `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>anolishq/renovate-config"]
}
```

## What the preset does

It extends `config:recommended` and turns on OSV vulnerability alerts. On top
of that:

- Automerge, once CI passes, covers non-major GitHub Actions updates
  (minor, patch, digest, pinDigest), non-major Python dev tooling (ruff,
  pytest, mypy, `types-*`), non-major npm `devDependencies`, and lock-file
  maintenance. Runtime dependency bumps and all majors open a PR for review.
- GitHub Actions are grouped and pinned to digests, and held for 3 days after
  release. npm and PyPI (`pep621`) updates get the same 3-day hold.
- `anolishq/.github` is exempt from the hold, so first-party workflow
  releases are adopted at once.
- The `ubuntu` runner image in `.github/workflows/release.yml` is not updated.
  Release binaries link libc and libstdc++ from the build runner, and a newer
  runner can build binaries that do not load on the deployed Pi (Debian 13).
  Move a release runner by hand, after checking the binaries' GLIBC and
  GLIBCXX versions against the Pi. Other workflows still get new runners.
- Lock-file maintenance runs before 6am on Monday.
- A custom manager bumps the anolishq release tag in schema URLs of the form
  `raw.githubusercontent.com/anolishq/<repo>/v<version>/` in any
  `doc-schemas.json`.
- `requirements.txt` and `requirements-lock.txt` are ignored.

## CI

[`.github/workflows/ci.yml`](.github/workflows/ci.yml) runs
`renovate-config-validator --strict` on `default.json` and `renovate.json` for
every push and pull request to `main`.

## Repo-specific rules

Put rules that apply to one repo in that repo's `renovate.json`, after the
`extends` line: version blocks, ecosystem groupings, or a custom manager for a
version pinned in its docs. Change this preset only for rules every repo
should get.
