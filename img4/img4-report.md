# IM4P component metadata inventory — iOS 27.3

## Method

`tools/windows/Run-Im4pInventory.ps1` used `blacktop/ipsw` v3.1.743 on Windows x86_64 to extract the 36 `.im4p` entries listed by the local ZIP inventory. Every extracted component was independently checked against the inventory's uncompressed byte count and full SHA-256 before calling `ipsw img4 im4p info --json`.

Only the IM4P type/tag, version, declared data length, compression field, and encryption boolean were saved. Payload bytes were not decompressed, decrypted, or analyzed; no key lookup was attempted.

## Results

- 36 of 36 entries parsed successfully.
- Types: 35 distinct type codes; `dcpf` occurred twice, all other codes once.
- Parser encryption flags: 35 `false`, 1 `true`.
- The one `encrypted=true` record is `Firmware/all_flash/sep-firmware.d17.RELEASE.im4p`, type `sepi`, declared data size 7,471,104 bytes.

These are parser-reported container metadata only. They do not authenticate the firmware, validate payload semantics, or establish a security impact.

## Evidence

- `research/ios-27.3/img4/im4p_inventory_summary.json`
- `research/ios-27.3/logs/im4p-inventory-run.txt`
- Extracted components are local-only under `research/ios-27.3/data/im4p/`.
