# Verification — FilmCraft rebuilt for Ubuntu 20.04 (glibc 2.31)

All builds ran on a genuine Ubuntu 20.04.5 (glibc 2.31-0ubuntu9.9) user-space rootfs,
rustc 1.99.0 / cargo 1.99.0, `cargo build --release --locked` at the pinned upstream tag,
packaged with each repo's own `packaging/linux/package.sh --formats appimage`.
Every check below ran inside the 20.04 environment; a build is only released if all checks pass.

Identical, byte-for-byte, to the FilmCraft AppImage shipped in the suite release (built once, verified once; same SHA256 in both repos).

| app | version | upstream tag | max GLIBC | max GLIBCXX | ldd | smoke test | SHA256 | status |
|---|---|---|---|---|---|---|---|---|
| filmcraft | 0.6.0 | v0.6.0 | 2.30 | — | pass | CLI --version exit 0; GUI alive after 8s under xvfb | `c25ac9cc085fc16690102fe4cfb4a3f17598d2d0a6e0916d235b628be808e41b` | PASS |

Checks performed for every AppImage:
1. `--appimage-extract`, then `objdump -T` over every ELF file; highest glibc symbol must be ≤ 2.31
   (GLIBCXX ≤ 3.4.28 when present). Result: GLIBC_2.30 everywhere — no GLIBCXX at all (pure Rust).
2. `ldd` on the main binaries: no "not found".
3. Smoke tests: CLI `--version` exit 0; GUI kept alive ~8 s under `xvfb-run` in the 20.04 rootfs
   (llvmpipe software GL; wgpu/eframe start fine — at runtime prefer real GL/Vulkan, see README).
4. SHA256 recorded above and shipped as `SHA256SUMS`.

Note: official upstream AppImages are built on Ubuntu 22.04 and top out at GLIBC_2.35; these
rebuilds top out at GLIBC_2.30, which is why they run on 20.04.
