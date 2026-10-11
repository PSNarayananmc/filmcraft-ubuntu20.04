# FilmCraft — Ubuntu 20.04 AppImage build

This repo rebuilds [FilmCraft](https://github.com/storytold/filmcraft) as an
AppImage that runs on **Ubuntu 20.04 LTS (glibc 2.31)**.

Upstream builds on `ubuntu-22.04` (glibc 2.35), so its AppImage fails on 20.04
with `GLIBC_2.3x not found`. We fix that by building inside an `ubuntu:20.04`
container on GitHub Actions.

## What's in this repo

| File | Purpose |
|------|---------|
| `.github/workflows/build.yml` | The build pipeline |
| `README.md` | This file |
| `LICENSE-MIT`, `LICENSE-APACHE`, `NOTICE` | Copied from upstream (same license) |

**No source code is stored here.** The workflow clones upstream at build time.

## How it works

1. Runs on `ubuntu-22.04` GitHub runner with `container: ubuntu:20.04`
   (because GitHub retired `ubuntu-20.04` runners in March 2025).
2. Installs all build deps inside the 20.04 container.
3. Installs Rust stable (≥ 1.95, the upstream `rust-version`).
4. Clones upstream at the requested ref (default: latest tag).
5. Runs upstream's `packaging/linux/package.sh --formats appimage`.
6. Verifies the result:
   - Every bundled ELF needs **GLIBC ≤ 2.31**.
   - `ldd` on the main binary has no unexpected "not found".
   - `filmcraft-cli --version` exits 0.
7. Renames to `FilmCraft-<version>-ubuntu20.04-x86_64.AppImage`, writes
   `SHA256SUMS`, uploads as both a workflow artifact and a GitHub pre-release.

## Trigger a build

GitHub → Actions → **Build FilmCraft AppImage for Ubuntu 20.04** → Run workflow.

You can enter a specific upstream tag (e.g. `v0.6.0`) or leave it as `latest`.

## Download

Releases are at <https://github.com/PSNarayananmc/filmcraft-ubuntu20.04/releases>.

## Install on Ubuntu 20.04

```bash
chmod +x FilmCraft-*-ubuntu20.04-x86_64.AppImage
./FilmCraft-*-ubuntu20.04-x86_64.AppImage
```

**GPU:** FilmCraft uses wgpu → Vulkan on Linux. Install a Vulkan driver:
```bash
sudo apt install mesa-vulkan-drivers   # Intel / AMD
# OR the NVIDIA proprietary driver
```

## License

MIT OR Apache-2.0 — same as upstream FilmCraft.
