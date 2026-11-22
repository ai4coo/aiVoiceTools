# Code signing policy

This page says how aiVoiceTools builds are signed and how to check that a download is the one we
published.

## Who builds and publishes

aiVoiceTools is made by Take Two Partners, LLC. Every build is made from our own source on our own
computers and uploaded by us to this repository's [Releases](../../releases). Builds published
anywhere else are not ours.

## How each build is signed

| Build | Signature |
|---|---|
| Mac (`.dmg`) | Signed ad hoc. It is not signed with an Apple Developer ID and not notarized, so macOS asks you to confirm the first time you open it (see [Install](README.md#install)). |
| Windows (`.msi` and `.zip`) | Not signed. Windows may show "Windows protected your PC" or "Unknown publisher" the first time; choose **More info**, then **Run anyway**. |
| Linux (`.deb`) | Not signed. Install it with `sudo apt install ./<file>.deb`. |

If we start signing a platform's builds, this page will say so, with the name the signature
carries, from that release on.

## Checking a download

Each release has a `SHA256SUMS` file listing the SHA-256 checksum of every build in it. Compare it
with the checksum of the file you downloaded:

- Mac and Linux: `shasum -a 256 <file>` (or `sha256sum <file>` on Linux)
- Windows (PowerShell): `Get-FileHash <file> -Algorithm SHA256`

If the checksum does not match the one in `SHA256SUMS`, do not run the file.

## What the builds contain

The builds hold aiVoiceTools and the open source software listed in
[THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md), and nothing else. They do not install other
programs, browser extensions or services, and they do not change system settings beyond what an
install normally does.

## Reporting a problem

If you find a build that looks tampered with or a checksum that does not match, open an issue on
this repository.
