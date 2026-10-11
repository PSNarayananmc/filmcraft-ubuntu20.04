# FilmCraft — Ubuntu 20.04 (glibc 2.31) rebuild

Rebuild of [FilmCraft](https://github.com/storytold/filmcraft) v0.6.0 — the open-source,
clean-room reimplementation of Adobe Premiere Pro, built in pure Rust — as an AppImage
that runs on **Ubuntu 20.04 LTS (glibc 2.31)**.

Upstream builds its AppImages on `ubuntu-22.04`, so they top out at **GLIBC_2.35** and
fail on 20.04 with `GLIBC_2.34`/`GLIBC_2.35 not found`. This rebuild was produced on a
genuine Ubuntu 20.04.5 environment and tops out at **GLIBC_2.30** (verified: glibc
symbol scan over every ELF file, `ldd` clean, CLI `--version` exit 0, GUI alive under
`xvfb` — full table in [`verification.md`](verification.md)).

## Download

**[Release v0.6.0-ubuntu20.04](https://github.com/PSNarayananmc/filmcraft-ubuntu20.04/releases/tag/v0.6.0-ubuntu20.04)**
(pre-release) — `filmcraft-0.6.0-ubuntu20.04-x86_64.AppImage` (+ `.zsync`,
`SHA256SUMS`, `verification.md`).

```sh
chmod +x filmcraft-0.6.0-ubuntu20.04-x86_64.AppImage
./filmcraft-0.6.0-ubuntu20.04-x86_64.AppImage
```

The identical file (same SHA256, built once) is also in the full-suite release:
[artcraft-launcher-ubuntu20.04 @ v0.2.0-suite-ubuntu20.04](https://github.com/PSNarayananmc/artcraft-launcher-ubuntu20.04/releases/tag/v0.2.0-suite-ubuntu20.04-r2).

## GPU note

FilmCraft renders with egui/eframe (GL/wgpu). On machines without working 3D
acceleration, install a driver first:

- NVIDIA: the proprietary driver (`ubuntu-drivers install`), which provides Vulkan + GL.
- AMD/Intel: Mesa — `sudo apt install mesa-vulkan-drivers libgl1-mesa-dri` on 20.04.

## What's in this repo

| File | Purpose |
|---|---|
| `README.md` | This file |
| `verification.md`, `SHA256SUMS` | Verification table and checksums (same as release assets) |
| `PATCHES.md` | Source changes: **none**; environment adaptations documented |
| `workflows-staging/filmcraft-build.yml` | CI build + verify + release pipeline, ready for `.github/workflows/` (see PATCHES.md) |
| `scripts/verify-appimage.sh` | The per-build verification used for the released AppImage |
| `LICENSE-APACHE`, `LICENSE-MIT`, `NOTICE` | Copied from upstream, unmodified (identical files) |
| `ATTRIBUTION.md` | Upstream asset attribution |

**No source code is stored here.** Builds clone upstream at the pinned tag
(`v0.6.0` = `2ff3f5e2…`) at build time; AppImages are never committed.

## Credit and licence

All credit for FilmCraft goes to the upstream authors:
https://github.com/storytold/filmcraft (website: https://getartcraft.com).
FilmCraft is dual-licensed under Apache-2.0 and MIT — upstream licence files are kept
here unmodified. This repository distributes **unmodified upstream source built on
glibc 2.31**; patches (none) are listed in [`PATCHES.md`](PATCHES.md). Product names
like Premiere Pro® are Adobe trademarks, mentioned only to describe what FilmCraft
replaces; FilmCraft is independent and not affiliated with or endorsed by Adobe.
