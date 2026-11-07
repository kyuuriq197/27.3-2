# iOS 27.3 IPSW research — interim summary

## Target

Local file: `C:\Users\Filimoshki\Downloads\iPhone14,5_27.3_24C5037i_Restore.ipsw`  
Observed file size: 11,483,532,526 bytes.

The IPSW's `BuildManifest.plist`, `Restore.plist`, and `SystemVersion.plist` identify the product as `iPhone14,5`, version `27.3`, build `24C5037i`; the manifest reports build train `RaveCSeed` and device class `d17ap`. Apple's [Developer Releases page](https://developer.apple.com/news/releases/) independently lists iOS 27.3 beta (24C5037i), dated November 3, 2026. This corroborates the release label/build, but does not authenticate the exact local IPSW bytes or its device-specific manifest.

## Work completed

- Audited the local source snapshot and its existing iOS 27.2 reports.
- Confirmed two restore ramdisk paths in the target manifest: `141-93054-032.dmg` and `141-92811-032.dmg`.
- Added Windows PowerShell tooling to record machine state, parse basic plist metadata, enumerate ZIP members, calculate component and archive SHA-256 values, and compare the resulting index with the saved previous-release inventory.
- Added execution and methodology documentation.
- Ran the tools successfully on Windows PowerShell 5.1. The archive has 113 entries, 11,639,139,444 expanded bytes, and SHA-256 `b14c3a1d6e6267fc0bac209140d084799255d818cb84b3f23d2cc9e0036486cc`.
- Independently cross-checked `ipsw info --json` output against local plist identity fields: version, build, product, and board agree. This is a second parser of the same archive, not provenance validation.
- Compared 113 target entries with all 96 saved 27.2 inventory rows. The name-based index records 33 additions, 16 removals, 19 same-name size changes, 23 same-name changed hash prefixes, and 38 same-name size/prefix matches.
- The archived previous IPSW is labeled `iPhone14,5_27.2_24B5099f_Restore.ipsw`. A local search of Downloads/Desktop found no copy; the IPSW metadata catalog has no record for the exact build, and Apple's release page has no exact-ID match. The catalog exposed 27.2 build `24B119`, which is different and was not downloaded or substituted.
- Stream-counted the previous 27.2 system and Cryptex listing records and confirmed the saved totals (519,777 and 3,085, respectively).
- Also stream-counted the previous kernel and ramdisk string dumps (116,614 and 217,451 lines) and verified the saved kext list has 269 data rows. These checks validate archived artifact counts only, not the original extraction methods or completeness.
- Cross-checked BuildManifest SHA-384 digests for selected target entries: kernel cache, both restore ramdisks, and AppOS matched; the system and Cryptex SystemOS AEA entry digests did not match a direct SHA-384 over their archived bytes. The discrepancy remains unexplained and is documented, not labeled corruption.
- Expanded the check to all 154 manifest component-reference rows: 124 matched the direct whole-entry digest and 30 did not. The 30 rows represent 15 unique component/path combinations repeated across both build identities; role-aware digest semantics are not implemented, so this is not an authenticity verdict.
- Installed the pinned Windows x64 `blacktop/ipsw` v3.1.743 release after verifying the GitHub release archive SHA-256. Extracted the kernel cache and parsed Mach-O metadata: XNU `13432.60.57~17`, 64-bit ARM64e fileset, 265 load commands, UUID `42A494C1-C9C1-5292-D885-F2F91A806121`.
- Parsed 269 unique 27.3 kext bundle paths and compared them with the saved 27.2 list: 269 shared paths, 0 additions/removals, 135 version-string changes and 134 exact version-string matches. No kext binary diff was performed.
- Extracted both restore ramdisk IM4P entries and verified their SHA-256 values against the ZIP inventory. The payloads are unencrypted APFS volumes; two read-only readers agree on their reachable paths and entry kinds (Erase: 2,024 entries; Upgrade: 2,411). The readers disagree on many file-size fields, so byte totals are parser-reported and not independently verified. No filesystem was mounted.
- Parsed the info metadata for both AEA containers; sizes and SHA-256 values match the IPSW inventory. The report retains only WKMS field names, not authentication or wrapped-response values; no key lookup or decryption was attempted.
- Extracted and hash-verified the AppOS image; its first 4 KiB APFS superblock has a valid Fletcher64 checksum (object ID 1, transaction ID 15). A format-aware read-only walk handled the sealed, hashed APFS tree: 128 nodes, 6,629 records, 127/127 child hashes valid, and 1,365 reachable paths. Independent `hadris-apfs 3.0.0-rc.3` output matched all paths and kinds and all available nonzero sizes. The lower-bound sum of available file sizes is 4,800,424 bytes; 1,008 file paths have no available inode data-stream size, so this is not total volume content size.
- AppOS OID 1172 maps to a checksum-valid `INTEGRITY_META` object with version 2, SHA-256, root-hash offset 128, and `broken_xid=0`. This record does not mark the seal broken, but the Merkle root was not recomputed or validated. OID 1173 remains unmapped.
- Attempted arm64e dyld extraction using the pinned Windows tool; it explicitly reports that dyld extraction is unsupported on Windows. No cache or framework inventory is claimed.
- Parsed IM4P metadata for 36 firmware components after verifying each extracted entry's size and SHA-256 against the ZIP inventory. All parsed; one SEP firmware payload is marked encrypted by the parser. No payload decryption or semantic firmware analysis was performed.
- Parsed five trustcaches with 4,754 declared entries total; parser counts matched parsed records, and no duplicate hashes were observed within each cache. Individual code-signing hashes remain in ignored local parser output and are not included in portable reports.
- Extracted, hash-verified, and decoded all 22 inventory-listed firmware plists. The portable result records schemas and top-level field names only; plist values remain in ignored local data.
- Verified all three 229-byte root-hash sidecars against the ZIP inventory and recorded their literal `IM4P`/component markers. No root-hash or APFS semantics were inferred.
- Verified and unwrapped the SystemOS canonical-metadata `msys` IM4P sidecar; its `pbze` payload contains the `bvx2` marker at offset 28. LZFSE decompression and Apple Archive parsing were not performed.
- Added a Cryptex summary covering all six `Cryptex1` BuildManifest roles across both restore identities, the 27.3 AEA container metadata, its `cssy` root-hash wrapper, and the SystemTrustCache (131 entries, no duplicate CDHashes). Five component paths match their direct BuildManifest SHA-384 ZIP-entry digest; the encrypted `SystemOS` component remains an unexplained mismatch, not a corruption/authenticity finding.
- Re-validated the archived 27.2 Cryptex listing structure: 3,085 records (893 directories, 2,190 files, 2 symlinks), no malformed rows; summarized extensions and largest files. This is archived 27.2 evidence only and does not provide a 27.3 Cryptex file listing.
- The archived 27.2 listing names 85 arm64e dyld-cache files totaling 7,400,102,867 bytes and 39 framework directory entries (2,059 file paths, 90,646,249 bytes). These are path/size aggregates only; neither the cache nor the framework binaries were parsed.
- Decoded all 37 LZFSE streams in the current, unencrypted 27.3 SystemOS canonical-metadata (`msys`) sidecar with pinned pure-Rust `lzfoo` 0.2.0. The first stream contains an `AA01` index with two bounded record headers (`D` and `F`); paths and hashes were not persisted. Decoded data totals 153,249,671 bytes. The other streams and trailing hash-tree material remain uninterpreted; this does not expose the encrypted Cryptex filesystem.

## Not yet established

- No full Apple signature/provenance validation has been performed.
- The linked 27.2 analysis reports a public FCS-key retrieval path for build `24B5099f`; this project has not tested that path for 27.3 or retrieved any key. Any claim that 27.3 necessarily requires additional authorization remains unverified.
- The kernel cache and restore ramdisk IM4P metadata have been parsed; the AppOS filesystem has a read-only, independently cross-checked path inventory. The tested `ferrosys` reader still refuses sealed volumes, but the Rust tree walkers succeeded. No AEA Cryptex volume was decrypted or inventoried.
- No AEA decryption, APFS mount, dyld/framework inventory, complete OS inventory, 27.3 Cryptex file analysis, or binary-level 27.2-to-27.3 comparison has been verified.
- The saved 27.2 inventory retains truncated 16-hex-character SHA-256 prefixes, and the exact `24B5099f` IPSW is not present in the checked local folders or discoverable through the queried public sources. Component-level comparisons beyond the kernel's separately recorded old full digest are therefore limited until the exact file or full old hashes are supplied.
- A public IPSW metadata lookup did not return this seed build. Apple's release listing confirms the iOS 27.3 beta build label/date, but no Apple-hosted checksum or signature chain was matched to the local IPSW.
- No vulnerability assessment was performed.

## Repository state

The selected local project directory is a source snapshot and has no `.git` metadata. Existing files are preserved. Branching, commits, and publication are therefore not performed from this folder.

## Validation status

The inventory and environment scripts ran successfully; syntax and metadata assertions passed. The local PowerShell execution policy required a process-scoped bypass, and no system policy was changed. Do not treat this interim report as a completed extraction or security analysis.
