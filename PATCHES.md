# PATCHES — source changes made for the FilmCraft Ubuntu 20.04 (glibc 2.31) rebuild

## Upstream source changes: **none**

FilmCraft v0.6.0 (upstream tag `v0.6.0`, commit `2ff3f5e266838700c292a35aaf762e3d4dcef980`)
was built from **pristine, unmodified upstream sources**:
`cargo build --release --locked -p filmcraft -p filmcraft-cli` against upstream's own
`Cargo.lock`, packaged with upstream's own `packaging/linux/package.sh --formats appimage`.
Nothing in the upstream repo was edited.

## Build-environment adaptations (no source edits)

| Adaptation | Why | Effect on binaries |
|---|---|---|
| Built inside an Ubuntu 20.04.5 user-space rootfs (proot, no Docker/root) — the sandbox equivalent of a `runs-on: ubuntu-22.04` + `container: ubuntu:20.04` CI job | Binaries must link against glibc 2.31 | Highest glibc symbol in `filmcraft` and `filmcraft-cli`: **GLIBC_2.30** (official 22.04 build: GLIBC_2.35 — verified with `objdump` on the official 0.6.0 AppImage) |
| `CARGO_PROFILE_RELEASE_LTO=false`, `CARGO_PROFILE_RELEASE_CODEGEN_UNITS=16`, `CARGO_PROFILE_RELEASE_DEBUG=false` (env overrides; upstream default `lto = "thin"`, `codegen-units = 1`) | Build sandbox has 2 cores / 4 GB RAM; thin-LTO link does not fit | Optimization level unchanged (`opt-level = 3`); possibly marginally larger/slower than the LTO build; runtime behaviour identical |
| `appstream` validator removed from the build rootfs | 20.04's `appstreamcli` 0.12 rejects tags in upstream's own metainfo (`branding`, `developer`, `display_length`) that 22.04's 0.15 accepts; upstream CI validates the same file on 22.04 | None; `desktop-file-validate` passed; metainfo shipped unmodified |
| `APPIMAGE_EXTRACT_AND_RUN=1` for `appimagetool` | No FUSE available | None |

## CI workflow: staged, not active

The PAT used for this rebuild cannot create files under `.github/workflows/` (GitHub
rejects the push without the `workflow` scope), so the FilmCraft build + verify + release
pipeline is staged at [`workflows-staging/filmcraft-build.yml`](workflows-staging/filmcraft-build.yml).
To activate, move it to `.github/workflows/` with a token that has the workflow scope
(or add it via the GitHub web editor). It builds from upstream at the pinned tag inside
`container: ubuntu:20.04`, verifies (glibc scan / ldd / smoke / SHA256) and publishes a
**brand-new** pre-release tag (collision-checked, never overwrites).

## Also rebuilt

The identical FilmCraft 0.6.0 AppImage (same SHA256) is also part of the full suite
release: https://github.com/PSNarayananmc/artcraft-launcher-ubuntu20.04/releases/tag/v0.2.0-suite-ubuntu20.04
