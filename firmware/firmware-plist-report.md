# Firmware plist metadata inventory — iOS 27.3

## Method

`tools/windows/Run-FirmwarePlistInventory.ps1` extracted the 22 inventory-listed `Firmware/*.plist` files using the pinned Windows `ipsw` tool. For every file, size and SHA-256 were verified against `firmware/ipsw_inventory.csv` before decoding with `ipsw plist`.

All 22 plists parsed successfully. The portable JSON records archive path, byte count, SHA-256, JSON root type, and top-level field names only. Decoded values remain in ignored local data under `research/ios-27.3/data/firmware-plists/decoded/`.

## Selected structure observations

- The SEP firmware metadata plist contains two top-level component keys: `rsep` and `sepi`.
- The cellular firmware plist has 13 top-level keys, including `SBL1-Version` and `RestoreSBL1-Version`; digest and hash-table field values are not copied into the report.
- The Veridian firmware map has four top-level keys: `board_id`, `digest_der`, `hw_version`, and `product_id`.
- Its nested `digest_der` object has `Digest` and `payload` fields; `hw_version` contains one version-keyed object. Only this structure was inspected; digest values and firmware payloads were not interpreted.
- The remaining plist names and field lists are in `firmware/firmware_plist_inventory.json`.

Field names and file types are metadata observations only. This does not verify firmware signatures, decode embedded hash-table values, establish device behavior, or constitute a security assessment.

## Evidence

- `research/ios-27.3/firmware/firmware_plist_inventory.json`
- `research/ios-27.3/logs/firmware-plist-inventory-run.txt`
- The extracted source plists and decoded JSON are local-only under `research/ios-27.3/data/firmware-plists/`.
