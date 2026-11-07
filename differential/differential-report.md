# iOS 27.2 vs 27.3 — archive inventory differential

## Inputs

- Current: `iPhone14,5_27.3_24C5037i_Restore.ipsw`, inventoried locally with full SHA-256 per ZIP member.
- Previous: `work/ipsw_inventory.txt`, a 27.2 inventory with 96 entries and only 16 hexadecimal characters (64 bits) retained from each component SHA-256.
- Comparison key: exact ZIP member name. Old/new component renames are counted as removals and additions; no role-aware matching is asserted by the script.

## Results

| Classification | Count | Interpretation |
|---|---:|---|
| Added names | 33 | Names exist in the 27.3 inventory, not the old index |
| Removed names | 16 | Names exist in the old index, not the 27.3 inventory |
| Size changed | 19 | Same-name entries have different reported uncompressed sizes |
| Hash-prefix changed | 23 | Same-name, same-size entries have a different first 64 bits of SHA-256 |
| Same size and hash prefix | 38 | Inventory-level match only |

The counts partition 129 names in the union of both indexes (96 previous + 113 current - 80 common names).

## Notable confirmed archive observations

- `kernelcache.release.iphone14`: 21,320,846 bytes in the previous inventory vs 21,518,572 bytes in 27.3. The old report contains the full entry SHA-256 `4d421e656e8c4b7b60565e6d83a458e66ebb870de147cc7a4168a4231dd23d40`; the new entry SHA-256 is `8d4132125c99892dddcb16f9fbcf2e0fa86c740ff70a762e3aa9209bf9181c2b`. This establishes changed bytes and size, not functional or security impact.
- The previous system-image filename `141-56854-039.dmg.aea` (8,321,499,136 bytes) is absent by name; `141-93752-026.dmg.aea` (8,455,716,864 bytes) is present in 27.3. The Cryptex image path likewise changes from `141-56012-039.dmg.aea` to `141-94047-030.dmg.aea`; both inventories report 2,415,919,104 bytes. The old inventory does not provide a matching full digest for renamed components, so equality/difference is not established by this comparison.
- The 27.2 erase/update ramdisk filenames differ from the 27.3 filenames, although the erase and update role/size pairs match (245,366,811 and 247,463,963 bytes, respectively). This does not establish equivalent contents.
- `BuildManifest.plist` changes from 333,320 to 333,326 bytes.

## Kernel and kext metadata

- The Windows parser reports XNU `13432.60.57~17` and Mach-O UUID `42A494C1-C9C1-5292-D885-F2F91A806121` for 27.3. The prior 27.2 report records XNU `13432.40.177.0.3~16` and UUID `7edbfbc03894a84a15ac3538a1383e3c`. These are changed metadata values; no functional or security conclusion follows.
- The prior and new kext lists each contain 269 unique paths. Path comparison found no additions or removals; version strings differ on 135 paths and match on 134. Because the old list is text-only and current output comes from a different parser, these metadata differences are not binary comparisons.
- The prior 27.2 report describes 2 `KernelKit`, 221 `kext`, and 46 `PlugIn`; the 27.3 parser labels all 269 rows `KEXT`. These parser-specific categories are not directly comparable.

## Limits

The old index's truncated digests are insufficient for robust full-digest comparisons; the original 27.2 IPSW is not available beside the target. No binary-level diff, Mach-O metadata comparison, kext comparison, framework/service/configuration comparison, APFS comparison, or functional-change analysis has been performed. A changed size or digest is not a proven functional change.

Machine-readable results: `ipsw_file_inventory_diff.csv` and `ipsw_file_inventory_diff_summary.json`.
