# Releasing a mod

Pull requests and releases use different builds.

Pull requests run on Linux and Windows. Linux always uses
`hemtt build --no-bin`. Windows pull requests do the same because private
build assets are never exposed to contributor code. A trusted push to `main`
performs the full binarized build.

A production tag never falls back to `--no-bin`. The release stops if Arma 3
Tools, a Workshop ID or the signing keys are missing.

## Arma 3 Tools

The ZIP is stored as the `arma3-tools.zip` asset of the private
`Sam-DarkBall-Mods/build-assets` release `arma3-tools-2026-08`. The read-only
`BUILD_ASSETS_TOKEN` organization secret is limited to that repository. CI
checks the pinned SHA-256 before extracting the archive to `C:\arma3tools`.

No one needs to run these EXE files by hand during a normal release. HEMTT uses
the binarization tools, and `tools/sign_release.ps1` calls
`DSSignFile.exe`.

## Signing keys

Create the production key on a trusted Windows computer with
`DSCreateKey.exe`. It produces two files:

- `name.biprivatekey` is private. Never commit it, attach it to an issue or
  send it through chat.
- `name.bikey` is public. Commit the mod's existing key as `keys/name.bikey`.

Keep an encrypted offline backup of both files. Private keys are stored as a
JSON object of base64 values in the `release-production` environment:

- `BI_PRIVATE_KEYS_JSON`

`tools/signing.json` maps release PBO names to their existing authorities. A
repository with several legacy PBO keys still uses one protected JSON secret.
The secret has the form `{"authority":"base64-private-key"}`.

The environment requires approval from `DarkBall123` before the Windows job
can read the secrets.

Each repository keeps its existing key pair. The workflow never generates,
renames or replaces the committed `.bikey`. It copies that exact file into
the release and fails if it does not verify the new `.bisign` files.

## Steam Workshop

Before creating a tag, set the following:

- `publishedid` in `meta.cpp`
- `STEAM_USERNAME` in the `steam-production` environment
- `STEAM_CONFIG_VDF` in the same environment

`STEAM_CONFIG_VDF` contains the saved Steam login token from a trusted local
Steam installation. Do not store the account password in GitHub.

The Steam job does not rebuild the mod. It downloads the ZIP already published
in the GitHub Release and uploads that exact content after owner approval.

## Release order

1. Update the HEMTT version and `meta.cpp`.
2. Merge the change into `main`.
3. Create a matching `vX.Y.Z` tag on the current `main` commit.
4. Approve `release-production`.
5. Check the PBO, `.bisign`, `.bikey` and SHA-256 file in the GitHub
   Release.
6. Approve `steam-production`.

## Files that are not shipped

HEMTT excludes editor backups, operating system metadata, temporary files and
authoring formats such as PSD, XCF and Blender files. Repository validation
also rejects backup and copy files inside `addons` and `optionals`.

Top-level GitHub files, tests and build scripts stay in the source repository.
Only the files listed in `.hemtt/project.toml` are copied beside the release
PBOs. Signing then adds the committed public key and generated `.bisign`
files.
