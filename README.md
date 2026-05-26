# Axelera Wingman Releases

This repository is the release-only distribution point for Axelera Wingman.

It contains release metadata only. GitHub source archives for tags in this
repository contain this small metadata tree. Installable desktop packages,
plugin bundles, checksums, SBOMs, and release manifests are attached to GitHub
Releases as assets.

Current release tag: `v1.1.0`

## Assets

- macOS Apple Silicon DMG: `Axelera Wingman-1.1.0-macOS-arm64.dmg`
- Linux Debian packages: `wingman_1.1.0_amd64.deb`,
  `wingman_1.1.0_arm64.deb`
- Claude Code, Codex, and Gemini extension archives
- `install-wingman.sh`, `SHA256SUMS`, `release-manifest.json`, and SBOM

## Install

Download the assets from the GitHub Release for `v1.1.0`.

On Linux, verify `SHA256SUMS`, then install the package for your architecture:

```bash
sudo dpkg -i wingman_1.1.0_amd64.deb
sudo apt-get install -f
```

Use the `arm64` package on ARM64 Linux systems.
