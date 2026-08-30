# Releasing a mod

Pull requests and releases use different builds.

Pull requests run on Linux and Windows. Linux always uses
`hemtt build --no-bin`. Windows performs a full binarized build when the
`ARMA3_TOOLS_URL` organization secret is available. Without that secret it
runs the same non-binarized build as Linux.

A production tag never falls back to `--no-bin`. The release stops if Arma 3
Tools, a Workshop ID or the signing keys are missing.

## Arma 3 Tools

`ARMA3_TOOLS_URL` must point to a private ZIP of the full Arma 3 Tools
directory. The ZIP is extracted to `C:\arma3tools` on the Windows runner, so
directories such as `BinMake`, `Binarize` and `DSSignFile` must be at the
root of the archive.

No one needs to run these EXE files by hand during a normal release. HEMTT uses
the binarization tools, and `tools/sign_release.ps1` calls
`DSSignFile.exe`.

## Signing keys

Create the production key on a trusted Windows computer with
`DSCreateKey.exe`. It produces two files:

- `name.biprivatekey` is private. Never commit it, attach it to an issue or
  send it through chat.
- `name.bikey` is public. It is copied into the `keys` directory of every
  release.

Keep an encrypted offline backup of both files. The CI copy is stored as two
base64 environment secrets in `release-production`:

- `BI_PRIVATE_KEY_B64`
- `BI_PUBLIC_KEY_B64`

The environment requires approval from `DarkBall123` before the Windows job
can read the secrets.

The same key can sign every Sam-DarkBall-Mods release. This is convenient for
server administrators because they install one public key. Separate keys per
mod limit the impact of a leaked key, but servers then need one key for every
mod. The workflow supports either choice.

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
PBOs.
