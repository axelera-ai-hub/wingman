# Axelera Wingman Releases

This repository is the release-only distribution point for Axelera Wingman.

It contains release metadata only. GitHub source archives for tags in this
repository contain this small metadata tree. Installable desktop packages,
plugin bundles, checksums, SBOMs, and release manifests are attached to GitHub
Releases as assets.

Current release tag: `v1.3.1`

## Assets

- macOS Apple Silicon DMG: `Axelera.Wingman-1.3.1-macOS-arm64.dmg`
- Linux Debian packages: `wingman_1.3.1_amd64.deb`,
  `wingman_1.3.1_arm64.deb`
- Claude Code, Codex, and Gemini extension archives
- Marketplace review payload:
  `axelera-ai-wingman-1.3.1-public-repo.tar.gz`
- `install-wingman.sh`, `SHA256SUMS`, `SHA256SUMS.sig`,
  `axelera-wingman-release-public-key.asc`, `release-manifest.json`, and SBOM

## Install

Download the assets from the GitHub Release for `v1.3.1`.

For marketplace review, extract
`axelera-ai-wingman-1.3.1-public-repo.tar.gz`. The repository root and
GitHub-generated source archives are intentionally metadata-only and are not the
reviewable plugin payload.

Simple install on Ubuntu or Debian: download the `.deb` for your machine
(`amd64` for Intel and AMD, `arm64` for ARM), double-click the downloaded
file, and choose Install when the software installer opens. No terminal is
needed.

For a verified install, use the signed installer instead of passing a
user-owned package directly to root apt. Download the four named assets below
from this release, then run the entire fail-closed block as one command:

```bash
(
  set -euo pipefail
  umask 077
  expected=6230D3CDBF7A0F3F60F72BDC75C0A17CBA2288F9
  verify_home="$(mktemp -d)"
  trap 'rm -rf "$verify_home"' EXIT HUP INT TERM
  export GNUPGHOME="$verify_home/gnupg"
  mkdir -m 700 "$GNUPGHOME"
  actual="$(gpg --no-options --no-autostart --batch --with-colons \
    --import-options show-only --import \
    axelera-wingman-release-public-key.asc 2>/dev/null \
    | awk -F: '$1 == "fpr" { print toupper($10); exit }')"
  test "$actual" = "$expected"
  gpg --no-options --no-autostart --batch \
    --import axelera-wingman-release-public-key.asc >/dev/null 2>&1
  status="$(gpg --no-options --no-autostart --batch --status-fd 1 \
    --verify SHA256SUMS.sig SHA256SUMS 2>/dev/null)"
  signer="$(awk '$1 == "[GNUPG:]" && $2 == "VALIDSIG" \
    { print toupper($3) }' <<<"$status")"
  test "$signer" = "$expected"
  installer_hash="$(awk '$2 == "install-wingman.sh" || \
    $2 == "*install-wingman.sh" { print tolower($1) }' SHA256SUMS)"
  [[ "$installer_hash" =~ ^[0-9a-f]{64}$ ]]
  test "$(sha256sum install-wingman.sh | awk '{ print tolower($1) }')" \
    = "$installer_hash"
  bash ./install-wingman.sh --version 1.3.1
)
```

Required files are `install-wingman.sh`, `SHA256SUMS`,
`SHA256SUMS.sig`, and `axelera-wingman-release-public-key.asc`.
Never pipe the installer into a shell or run these verification lines as
independent interactive commands.
