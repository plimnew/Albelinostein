# How to verify a download

This is to confirm that the file you downloaded is the original, published
by Albelinostein, and that nobody altered it along the way.

> **Current status.** Version 0.1.0 was published on 26 November 2026, only
> on the downloads page of the site
> (<https://albelino.plimswitzzer.com/en/downloads/>). Each published file
> has its signature file (`.asc`) and the public key is published. See the
> [state of each version](../README.en.md).

## What proves what

| Check | What it proves |
|---|---|
| SHA-256 checksum equal to the one in `SHA256SUMS` | The file arrived whole and is the one on the list |
| Signature of `SHA256SUMS` (`SHA256SUMS.asc`) | The list was made by whoever holds the "Albelino Releases" key |
| Both together | The file is the original |

The checksum alone is not enough: whoever altered a file on a server could
also alter the list. It is the signature that closes the circle.

## Where the key and the files are

The public key is published in this repository, in `docs/keys/`, and on the
site, at <https://albelino.plimswitzzer.com/downloads/releases/public-key.asc>.
The fingerprint is 44E5 30D3 FCDF B80C 9EAB F680 5EB9 C85F 1DA4 BC91. The
folder contains:

| File | What it is |
|---|---|
| `albelino-releases.asc` | The GPG public key "Albelino Releases", which signs `SHA256SUMS` and each file listed in it |
| `albelino-releases.fingerprint` | Its fingerprint, to compare after importing |
| `android-cert-sha256.txt` | The SHA-256 checksum of the certificate that signs the Android APK |

Version 0.1.0 has its `SHA256SUMS` and `SHA256SUMS.asc` on the downloads
page of the site
(<https://albelino.plimswitzzer.com/downloads/releases/0.1.0/SHA256SUMS> and
<https://albelino.plimswitzzer.com/downloads/releases/0.1.0/SHA256SUMS.asc>).
The Releases of this repository contain no version.

## Three steps

You need `gpg` (Linux: already included; macOS: `brew install gnupg`;
Windows: Gpg4win).
Download into the same folder the file you want, `SHA256SUMS` and `SHA256SUMS.asc`.

**1. Get the public key, once.** It is in
`docs/keys/albelino-releases.asc`, in this repository.

```bash
gpg --import albelino-releases.asc
```

```bash
gpg --fingerprint "Albelino Releases"
```

The fingerprint shown must be the same as the one in
`docs/keys/albelino-releases.fingerprint`. If it is different, stop: the key
is not ours.

**2. Verify the list.**

```bash
gpg --verify SHA256SUMS.asc SHA256SUMS
```

It must say `Good signature from "Albelino Releases"`. The warning that the
key "is not certified with a trusted signature" is normal: it only means
that you yourself confirmed the fingerprint, in step 1.

**3. Verify the file.**

Linux:

```bash
sha256sum --check --ignore-missing SHA256SUMS
```

macOS:

```bash
shasum -a 256 --check --ignore-missing SHA256SUMS
```

Windows (PowerShell), comparing by hand with the file's line in `SHA256SUMS`:

```powershell
Get-FileHash .\Albelino_0.1.0_x64-setup.exe -Algorithm SHA256
```

The file must appear with `OK`, or with the same checksum.

## By platform

Each downloadable file will have its own GPG signature, named after the file
plus `.asc`, which is verified without the list:
`gpg --verify albelino_0.1.0_amd64.deb.asc albelino_0.1.0_amd64.deb`.
This applies to the Linux packages, the Windows installer, the `.apk`, the
`.dmg` and the command-line programs. (There may be `.sig` files next to
some installers; they are only for the application's own updater, they are
not the download's signature and you do not need to do anything with them.)

- **Linux** (`.deb`, `.rpm`, `.AppImage`, command line): verify with the
  three steps above and, if you like, also the package's `.asc`.
- **Android** (`.apk`): Android only installs an update signed with the same
  key as the installed version. To confirm before installing:
  `apksigner verify --print-certs Albelino_0.1.0_arm64-v8a.apk` and compare
  the certificate's `SHA-256 digest` with `docs/keys/android-cert-sha256.txt`.
- **Windows** (`-setup.exe`): does **not** yet have an Authenticode
  signature. Windows will show an "unknown publisher" warning; this is
  expected. Verify with the three steps above.
- **macOS** (`.dmg`): version 0.1.0 has no `.dmg`. From 0.1.1 on, the `.dmg`
  is only for Macs with **Apple Silicon** (M1 or later; it does not run on
  Macs with an Intel processor). It is signed with the Developer ID of Apple
  team `B4S6KAG56M` and notarised by Apple: it opens with a double click.
  Besides the three steps above, on a Mac, in Terminal, in the download
  folder, replacing FILE with the file's name: `xcrun stapler validate FILE`
  must say `The validate action worked!`, and
  `spctl -a -vv -t open --context context:primary-signature FILE` must answer
  `accepted` and `source=Notarized Developer ID`, with `(B4S6KAG56M)` in the
  `origin` line.

Windows, macOS and Android are platforms not yet validated; see the
[state of each version](../README.en.md).

## File names

| File | Platform |
|---|---|
| `albelino_<version>_amd64.deb` | Linux (Debian, Ubuntu) |
| `albelino-<version>-1.x86_64.rpm` | Linux (Fedora, openSUSE) |
| `Albelino_<version>_x86_64.AppImage` | Linux (any) |
| `Albelino_<version>_x64-setup.exe` | Windows |
| `Albelino_<version>_arm64-v8a.apk` | Android |
| `Albelino_<version>_<architecture>.dmg` | macOS |
| `adelino-cli-<version>-<target>.tar.gz` or `.zip` | Command line |

A file with `debug` or `unsigned` in its name is not a published version.

## If something does not match

Do not install. Delete the file and report it privately on GitHub, under
["Report a vulnerability"](https://github.com/plimnew/Albelinostein/security/advisories/new): a download that does not match may have
been tampered with.
