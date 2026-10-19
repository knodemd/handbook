# 7 - Releasing

How a version of Knode is cut, built, signed, notarised and published, so every release is done the
same way. The installs it produces are described in [decision 0007](decisions/0007-distribution.md).

## One version

The `knode` binary (the CLI, the MCP server and Knode Service) and the apps share **one version**.
It lives in one place, `version` under `[workspace.package]` in `knode/core/Cargo.toml`, and the apps
read the same number.

Versions follow [semantic versioning](https://semver.org): `MAJOR.MINOR.PATCH`.

- **Before 1.0**, the minor number is the milestone: `0.1.0` is v0.1, `0.2.0` is v0.2. Anything may
  still change between minor versions, including the `.knode` format and the MCP tools, and the
  release notes say how to move across.
- **Patch releases** (`0.1.1`) fix bugs and change nothing people rely on.
- **Pre-releases** add a suffix: `0.2.0-beta.1`, `0.2.0-rc.1`. They are published as pre-releases and
  Homebrew does not offer them.
- **From 1.0**, a breaking change to the format, the MCP tools or the CLI means a new major version.

## Changelog

`CHANGELOG.md` in the `knode` repository follows [Keep a Changelog](https://keepachangelog.com).

- Every pull request people would notice adds a line under `## [Unreleased]`, in the same pull
  request: what changed for them, not how it was built.
- Lines go under `Added`, `Changed`, `Fixed`, `Removed` or `Security`.
- When a version is cut, `## [Unreleased]` becomes `## [X.Y.Z] - YYYY-MM-DD` and a new empty
  `## [Unreleased]` goes on top.
- That section becomes the release notes. Notes are written for people: start with what is new and
  why it matters, then the details.

## Tagging

A release is a tag on the default branch of `knode`: `vX.Y.Z` (or `vX.Y.Z-beta.N`).

1. Open a pull request `chore(release): X.Y.Z` that sets the version in `core/Cargo.toml`, updates
   `core/Cargo.lock` and moves the changelog entries under the new version.
2. When it is merged, tag that commit with an annotated tag and push the tag:

   ```sh
   git tag -a vX.Y.Z -m "Knode X.Y.Z"
   git push origin vX.Y.Z
   ```

Tags are never moved or deleted once pushed. A broken release is fixed by a new patch version. There
are no release branches before 1.0; if an older version ever needs a fix, a `release/X.Y` branch is
cut from its tag.

## The release workflow

Pushing a tag runs `.github/workflows/release.yml` in the `knode` repository:

1. **Check the version.** The tag must match the version in `core/Cargo.toml`, or the release stops.
2. **Build** the `knode` binary with `cargo build --release --locked -p knode-cli`:

   | Target | Runner | Archive |
   |---|---|---|
   | `aarch64-apple-darwin` | macOS, Apple silicon | `knode-vX.Y.Z-aarch64-apple-darwin.tar.gz` |
   | `x86_64-apple-darwin` | macOS, Apple silicon (cross) | `knode-vX.Y.Z-x86_64-apple-darwin.tar.gz` |
   | `universal-apple-darwin` | both Mac builds joined with `lipo` | `knode-vX.Y.Z-universal-apple-darwin.tar.gz` |
   | `x86_64-unknown-linux-gnu` | Ubuntu 22.04, x86_64 | `knode-vX.Y.Z-x86_64-unknown-linux-gnu.tar.gz` |
   | `aarch64-unknown-linux-gnu` | Ubuntu 22.04, arm64 | `knode-vX.Y.Z-aarch64-unknown-linux-gnu.tar.gz` |

   Linux builds run on the oldest supported Ubuntu so the binary works with older versions of glibc.
3. **Sign and notarise** the Mac binaries (see below).
4. **Package** each binary with `LICENSE`, `NOTICE` and `README.md` as a `.tar.gz`, with a `.sha256`
   file next to it.
5. **Draft the GitHub Release** for the tag, with every archive, its `.sha256` and one `SHA256SUMS`
   file for all of them. The notes come from the changelog section for that version.

The release stays a **draft** until a maintainer has checked it and publishes it by hand. Nothing is
published automatically.

The Mac app joins the workflow when it ships: the app is archived with Xcode, signed, notarised,
stapled and attached as `Knode-X.Y.Z.dmg`, with its checksum in `SHA256SUMS`.

## Signing and notarisation

Everything that runs on a Mac is signed with the project's **Developer ID** and notarised by Apple,
so Gatekeeper opens it without warnings.

- **The binary** is signed with `codesign --options runtime --timestamp` (the hardened runtime) and
  sent to `notarytool`. A bare binary cannot be stapled; Gatekeeper checks its notarisation online.
- **The app** is signed with a Developer ID Application certificate, including the `knode` binary it
  carries, notarised, and the ticket is stapled to the app and the `.dmg`, so it opens offline too.
- **Notarisation** uses an App Store Connect API key, not a personal Apple ID password.

Signing material lives only in **GitHub Actions secrets** on the `knode` repository, never in a
repository, an issue or a log ([chapter 4](04-building-in-public.md)):

| Secret | What it holds |
|---|---|
| `MACOS_CERTIFICATE_P12` | The Developer ID Application certificate and its key, as a base64 `.p12` |
| `MACOS_CERTIFICATE_PASSWORD` | The password of that `.p12` |
| `MACOS_SIGNING_IDENTITY` | The identity to sign with, for example `Developer ID Application: Name (TEAMID)` |
| `APPLE_API_KEY_P8` | The App Store Connect API key, as a base64 `.p8` |
| `APPLE_API_KEY_ID` | The key's id |
| `APPLE_API_ISSUER_ID` | The issuer id of the App Store Connect team |

When the certificate secrets are missing, the workflow still builds and packages, skips signing and
notarisation, and warns about it. Forks and test tags work this way; a real release never ships
unsigned Mac builds.

The workflow imports the certificate into a temporary keychain that is deleted at the end of the job.
If a certificate or key leaks, revoke it at Apple first, then replace the secret.

## Homebrew

`knodemd/homebrew-tap` holds two files, both pointing at the release assets:

- `Formula/knode.rb`: the `knode` binary, from the per-platform archives (Apple silicon, Intel and
  Linux).
- `Casks/knode.rb`: the Mac app, from `Knode-X.Y.Z.dmg`. It has no `binary` stanza, so it never links
  a second `knode` when the formula is installed.

After the release is published, a pull request to the tap sets `version` and every `sha256` from
`SHA256SUMS`. Before it is merged, it is tested on a Mac:

```sh
brew tap knodemd/tap
cd "$(brew --repository knodemd/tap)" && gh pr checkout <number>
brew reinstall knode && brew test knode
brew audit --strict knode
brew reinstall --cask knode
```

## The download on knode.md

knode.md links to the latest release's `.dmg`, and `knode.md/install` lists every install command.
`https://knode.md/install.sh` (from v0.2) downloads the right archive for the platform from the latest
release and checks it against `SHA256SUMS` before installing. A new install method changes the page and
decision 0007 together.

## Checklist

Before the tag:

- [ ] The milestone's issues are closed or moved to the next one.
- [ ] `main` is green: the core checks and the app build pass.
- [ ] `CHANGELOG.md` has a section for the version, written for people.
- [ ] The version is set in `core/Cargo.toml` and `core/Cargo.lock`, merged through a pull request.

The release:

- [ ] Annotated tag `vX.Y.Z` on the merged commit, pushed.
- [ ] The release workflow passed and signed and notarised the Mac builds (no "not signed" warning).
- [ ] The draft release has every archive, every `.sha256`, `SHA256SUMS` and the app's `.dmg`.
- [ ] Download one archive, check it with `shasum -a 256 -c`, and run `knode --version`.
- [ ] On a Mac, `spctl --assess` accepts the app, and the app opens without a warning.
- [ ] Notes reviewed, then the release is published.

After:

- [ ] Tap pull request with the new version and checksums, tested, merged.
- [ ] `brew upgrade knode` and `brew upgrade --cask knode` install the new version.
- [ ] knode.md download and install page point at the new version.
- [ ] The milestone is closed and the release is announced.
