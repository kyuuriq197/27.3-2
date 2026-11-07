# Kernel extension inventory — status

## 27.3

The Windows x86_64 `blacktop/ipsw` v3.1.743 tool parsed the extracted kernel cache. It returned 269 records with 269 unique bundle paths and 269 unique bundle IDs. Its `package_type` field is `KEXT` for all rows. Machine-readable output: `kext_inventory_27.3.json`.

## Previous 27.2 evidence

The saved `work/kext_list.txt` contains 269 non-empty, non-comment rows. The prior kernel report categorizes these as 2 `KernelKit`, 221 `kext`, and 46 `PlugIn`; the new tool reports all rows under `package_type=KEXT`, so those category labels are not directly comparable.

## Path/version inventory comparison

The path-level comparison found 269 unique old paths and 269 unique new paths: no path additions or removals. The stored version strings differ for 135 paths and are identical for 134. These are metadata string observations only. The previous source is a text list, the releases were parsed by different tooling, and no kext binary was extracted or compared. A version string can be reformatted or changed without demonstrating a functional change.

Machine-readable output: `research/ios-27.3/differential/kext_inventory_diff.csv` and `kext_inventory_diff_summary.json`.

## Next validation needed

Compare full kext metadata from both releases and, where legally and safely appropriate, hash the corresponding extracted kext binaries. A changed count, name, or version string alone does not demonstrate changed behavior or a vulnerability.
