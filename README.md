# Axelera Wingman Releases

This repository is the release-only distribution point for Axelera Wingman.

It contains release metadata only. GitHub source archives for tags in this
repository contain this small metadata tree. Linux desktop packages, checksums,
SBOMs, and release manifests are attached to GitHub Releases as assets.

Current release tag: `v1.7.53`

## Install



Open a terminal and paste the whole block below. It downloads the package for this machine from the public GitHub release and installs it with apt.

```bash
(
  set -e
  d=$(mktemp -d); chmod 755 "$d"; cd "$d"
  sudo apt-get update || echo "Some package sources could not be refreshed; continuing." >&2
  sudo apt-get install -y curl ca-certificates
  v=1.7.53; a=$(dpkg --print-architecture)
  curl -fsSLO --retry 3 "https://github.com/axelera-ai-hub/wingman/releases/download/v$v/wingman_${v}_$a.deb"
  sudo apt-get install -y "./wingman_${v}_$a.deb"
  dpkg-query -W wingman
)
```

The last command checks the install: it prints wingman and the installed version. Supported systems: Ubuntu 20.04, 22.04, 24.04, 26.04 and Debian 12, 13, on Intel/AMD or ARM.

On a desktop, you can instead download the .deb for your machine (amd64 for Intel and AMD, arm64 for ARM), double-click it and choose Install.

### Verify signatures before installing (optional)

To check the release signature before anything is installed, paste this block instead of the one above. It first refreshes the package lists and installs curl, the CA certificates, GnuPG and python3, which the download, the signature check and the installer need and which minimal Ubuntu and Debian systems do not include. It then downloads the signed installer and its verification files, checks the release key, the signature and the checksum, and installs only when every check passes. Paste the whole block at once, so that a failed check stops the install.

```bash
(
  set -euo pipefail

  sudo apt-get update || echo "Some package sources could not be refreshed; continuing." >&2
  sudo apt-get install -y curl ca-certificates gnupg python3

  umask 077
  version=1.7.53
  expected_fingerprint=6230D3CDBF7A0F3F60F72BDC75C0A17CBA2288F9
  base="https://github.com/axelera-ai-hub/wingman/releases/download/v$version"
  work_dir="$(mktemp -d)"
  trap 'rm -rf "$work_dir"' EXIT HUP INT TERM
  cd "$work_dir"

  for asset in \
    install-wingman.sh \
    SHA256SUMS \
    SHA256SUMS.sig \
    axelera-wingman-release-public-key.asc; do
    curl --fail --silent --show-error --location \
      --proto '=https' --proto-redir '=https' --tlsv1.2 \
      --connect-timeout 15 --max-time 120 --max-filesize 10485760 \
      --output "$asset" "$base/$asset"
  done

  export GNUPGHOME="$work_dir/gnupg"
  mkdir -m 700 "$GNUPGHOME"
  actual_fingerprint="$(
    gpg --no-options --no-autostart --batch --with-colons \
      --import-options show-only \
      --import axelera-wingman-release-public-key.asc 2>/dev/null \
      | awk -F: '$1 == "fpr" { print toupper($10); exit }'
  )"
  test "$actual_fingerprint" = "$expected_fingerprint"
  gpg --no-options --no-autostart --batch \
    --import axelera-wingman-release-public-key.asc >/dev/null 2>&1
  signature_status="$(
    gpg --no-options --no-autostart --batch --status-fd 1 \
      --verify SHA256SUMS.sig SHA256SUMS 2>/dev/null
  )"
  valid_signer="$(
    awk '$1 == "[GNUPG:]" && $2 == "VALIDSIG" { print toupper($3) }' \
      <<<"$signature_status"
  )"
  test "$valid_signer" = "$expected_fingerprint"

  installer_hashes="$(
    awk '$2 == "install-wingman.sh" || $2 == "*install-wingman.sh" \
      { print tolower($1) }' SHA256SUMS
  )"
  test "$(wc -l <<<"$installer_hashes" | tr -d ' ')" = 1
  [[ "$installer_hashes" =~ ^[0-9a-f]{64}$ ]]
  test "$(sha256sum install-wingman.sh | awk '{ print tolower($1) }')" \
    = "$installer_hashes"

  bash ./install-wingman.sh --version "$version"
)
```

## Assets

Download the assets from the GitHub Release for `v1.7.53`.

- Linux Debian packages: `wingman_1.7.53_amd64.deb`,
  `wingman_1.7.53_arm64.deb`
- `install-wingman.sh`, `SHA256SUMS`, `SHA256SUMS.sig`,
  `axelera-wingman-release-public-key.asc`, `release-manifest.json`, and SBOM

`SHA256SUMS`, the release manifests, and the SBOM cover exactly the assets
attached to this Linux release.

## Support

Report problems with the Linux app as issues in this repository; the Wingman
team reads them. Questions and discussion belong on the Axelera community at
https://community.axelera.ai/voyager-wingman-64. Security problems follow
SECURITY.md and never go in a public issue.
