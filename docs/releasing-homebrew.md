# sag Homebrew Release Playbook

The `Release Binaries` workflow updates `steipete/homebrew-tap` after it uploads and verifies release assets. Do not edit the tap formula as a normal release step.

## Normal flow

1. Push the annotated `vX.Y.Z` tag after the release commit is on `main`.
2. Watch `Release Binaries` through its `update-homebrew-tap` job.
3. Confirm the dispatched `Update Formula` run succeeds in `steipete/homebrew-tap`.
4. Inspect `Formula/sag.rb`: its versioned artifact URLs and SHA-256 values must match the GitHub release assets and checksum manifest.
5. Sanity-check install from tap:

```sh
brew update
brew reinstall steipete/tap/sag
brew test steipete/tap/sag
sag --version
```

## Recovery

If the release assets exist but the tap update failed, fix the release workflow or tap workflow and rerun `Release Binaries` for the existing tag:

```sh
gh workflow run release-binaries.yml --repo steipete/sag -f tag=vX.Y.Z
```

The workflow redispatches `update-formula.yml` with the repository, tag, artifact template, and a unique request ID, then waits for the matching tap run. Verify the rerun and formula before installing. Manual formula edits are a last-resort repair and still require checksums from the exact published assets.
