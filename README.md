# redbull-bakasu-ci

Kernel CI for **Pixel 5 / 5a** (redbull: `bramble`, `redfin`) — [BakaSU](https://github.com/Baka-SU/BakaSU) root, with or without SUSFS.

Builds `Image.lz4` from [LineageOS/android\_kernel\_google\_redbull](https://github.com/LineageOS/android_kernel_google_redbull) (default branch, 4.19.325) with AOSP Clang 22, packs AnyKernel3 zips, and publishes a release.

## Output

```
redbull-<kernelrelease>-<date>.zip           BakaSU, manual hooks
redbull-<kernelrelease>-susfs-<date>.zip     BakaSU + SUSFS
SHA256SUMS.txt
```

Runs monthly (1st, 03:00 UTC) and on demand via `workflow_dispatch`.

## Layout

| Path                                         | What                                                                                              |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| `.github/workflows/build-redbull-kernel.yml` | the whole pipeline                                                                                |
| `patches/ksu_manual_hooks.patch`             | BakaSU manual hooks for the plain build                                                           |
| `patches/susfs-v2.3.0-4.19.patch`            | SUSFS v2.3.0 kernel side, 4.19 port ([source](https://github.com/fabiannabil1/susfs-4.19-nongki)) |
| `patches/susfs-v2.3.0-4.19-redbull.patch`    | hunks that differ on this tree                                                                    |

## Notes

- `KSU_SUSFS` and `KSU_MANUAL_HOOK` are one Kconfig choice: the SUSFS build enables only `KSU_SUSFS` (it ships the hooks) and skips the manual hooks patch.
- `KALLSYMS_ALL=y` is required — without it BakaSU demands several selinux symbols be un-`static`'d.
- SUSFS stays at **v2.3.0**: no 4.19 port of v2.4.0 exists.
- Patches are applied with `--fuzz=0` and the build fails on any reject. Do not add fuzz — it silently lands hunks in the wrong function on this tree.

⚠️ Flash the zip from a recovery and keep a backup of `boot`; these builds are not boot-tested by CI.
