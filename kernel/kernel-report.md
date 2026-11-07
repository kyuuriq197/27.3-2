# Kernel report — iOS 27.3

## Verified

- Archive entry: `kernelcache.release.iphone14`
- Input size: 21,518,572 bytes
- Input SHA-256: `8d4132125c99892dddcb16f9fbcf2e0fa86c740ff70a762e3aa9209bf9181c2b`
- Method: ZIP entry was streamed to SHA-256 by `tools/windows/Get-IpswInventory.ps1`.

## Windows static analysis

- Tool: `blacktop/ipsw` v3.1.743 Windows x86_64, downloaded from the official GitHub release and pinned by archive SHA-256 `4633aee6d8f6edbe23c8fe946c8000700f7761e18f34c33a15779d9932165cf8`.
- Extracted cache: `research/ios-27.3/data/kernel/24C5037i__iPhone14,5/kernelcache.release.iPhone14,5`
- Extracted artifact size: 68,354,048 bytes
- Extracted artifact SHA-256: `33e939038967cff30526d7e2ba7aca0ce3ad5ffc781b0794dca6cb796f356db1`
- `ipsw kernel version --json` reports Darwin `27.3.0`, XNU `13432.60.57~17`, `RELEASE`, `ARM64`, CPU `T8110`.
- `ipsw macho info --header --loads` reports 64-bit Mach-O `FILESET`, AARCH64/ARM64e caps `KER00`, 265 load commands, and `LC_UUID` `42A494C1-C9C1-5292-D885-F2F91A806121`. The output contains 9 `LC_SEGMENT_64` commands and 252 `LC_FILESET_ENTRY` commands.
- `ipsw kernel kexts --json` returned 269 records with 269 unique bundle paths and 269 unique bundle IDs. The output's `package_type` field is `KEXT` for all 269 rows.
- Segment names reported in the load-command output: `__TEXT`, `__PRELINK_TEXT`, `__DATA_CONST`, `__DATA_SPTM`, `__TEXT_EXEC`, `__TEXT_BOOT_EXEC`, `__PRELINK_INFO`, `__DATA`, and `__LINKEDIT`.

The tool's JSON mode does not support combining `--json` with `--header` or `--loads`; the attempted command emitted a warning and was replaced by the documented human-readable command. The raw TOC JSON remains available as `kernel_macho_info.json`.

## 27.2 comparison evidence

The saved 27.2 report claims a 67,518,464-byte decompressed arm64e `MH_FILESET` kernel, XNU string `xnu-13432.40.177.0.3~16/RELEASE_ARM64_T8110`, and `LC_UUID`/PrelinkKCID `7edbfbc03894a84a15ac3538a1383e3c`. These are prior reported results, not independently reproduced on the current Windows host.

The previous IPSW inventory reports a 21,320,846-byte kernel-cache entry with SHA-256 prefix `4d421e656e8c4b7b`; the prior kernel report records that full entry SHA-256 as `4d421e656e8c4b7b60565e6d83a458e66ebb870de147cc7a4168a4231dd23d40`. The 27.3 entry is 197,726 bytes larger and has a different full SHA-256. This confirms different archived bytes, not a functional or security change. The two saved XNU version strings and UUIDs also differ, which is a verified metadata difference; it does not establish a behavioral or security impact.

## Evidence files

- `research/ios-27.3/firmware/ipsw_inventory.csv`
- `work/kernel_report.txt` (prior 27.2 report)
- `research/ios-27.3/differential/ipsw_file_inventory_diff.csv`
- `research/ios-27.3/kernel/kernel_version.json`
- `research/ios-27.3/kernel/kernel_analysis_summary.json`
- `research/ios-27.3/kernel/kernel_macho_header_loads.txt`
- `research/ios-27.3/kext/kext_inventory_27.3.json`
- `research/ios-27.3/logs/kernel-analysis-run.txt`
