# Axelera Wingman Releases

This repository is the release-only distribution point for Axelera Wingman.

It contains release metadata only. GitHub source archives for tags in this
repository contain this small metadata tree. Installable desktop packages,
plugin bundles, checksums, SBOMs, and release manifests are attached to GitHub
Releases as assets.

Current release tag: `v1.2.0`

## Assets

- macOS Apple Silicon DMG: `Axelera.Wingman-1.2.0-macOS-arm64.dmg`
- Linux Debian packages: `wingman_1.2.0_amd64.deb`,
  `wingman_1.2.0_arm64.deb`
- Claude Code, Codex, and Gemini extension archives
- Marketplace review payload:
  `axelera-ai-wingman-1.2.0-public-repo.tar.gz`
- `install-wingman.sh`, `SHA256SUMS`, `SHA256SUMS.sig`,
  `axelera-wingman-release-public-key.asc`, `release-manifest.json`, and SBOM

## Install

Download the assets from the GitHub Release for `v1.2.0`.

For marketplace review, extract
`axelera-ai-wingman-1.2.0-public-repo.tar.gz`. The repository root and
GitHub-generated source archives are intentionally metadata-only and are not the
reviewable plugin payload.

On Linux, download the package for your architecture, verify `SHA256SUMS` with
`SHA256SUMS.sig`, then install with apt so dependencies are resolved in one
transaction:

```bash
gpg --import axelera-wingman-release-public-key.asc
gpg --verify SHA256SUMS.sig SHA256SUMS
sha256sum -c --ignore-missing SHA256SUMS
sudo apt install ./wingman_1.2.0_amd64.deb
```

Use the `arm64` package on ARM64 Linux systems.
