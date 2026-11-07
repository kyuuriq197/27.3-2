# Firmware inventory — iPhone14,5 / iOS 27.3

## Evidence and method

- Input: `C:\Users\Filimoshki\Downloads\iPhone14,5_27.3_24C5037i_Restore.ipsw`
- Tool: Windows PowerShell 5.1, .NET `System.IO.Compression`, `tools/windows/Get-IpswInventory.ps1`
- Operation: opened the ZIP read-only; parsed `BuildManifest.plist`, `Restore.plist`, and `SystemVersion.plist`; streamed every ZIP entry through SHA-256; hashed the whole archive.
- No extraction, signature verification, APFS mount, or decryption was performed.
- In a follow-up check, selected BuildManifest SHA-384 digests were recomputed directly over ZIP entry bytes. The kernel cache, both ramdisks, and AppOS DMG matched their manifest digests; the two large AEA images did not. The cause of those two mismatches is unresolved, so they are not considered verified against the manifest.

## Verified local results

| Property | Result |
|---|---|
| IPSW file size | 11,483,532,526 bytes |
| IPSW SHA-256 | `b14c3a1d6e6267fc0bac209140d084799255d818cb84b3f23d2cc9e0036486cc` |
| ZIP entries | 113 |
| Sum of uncompressed entry sizes | 11,639,139,444 bytes |
| Sum of compressed entry sizes | 11,483,514,840 bytes |
| Product type | `iPhone14,5` |
| Product version / build | `27.3` / `24C5037i` |
| Build train / device class | `RaveCSeed` / `d17ap` |
| Restore variants | `Developer Erase Install (IPSW)`; `Developer Upgrade Install (IPSW)` |
| Restore ramdisks | Erase: `141-93054-032.dmg`; Update: `141-92811-032.dmg` |
| Kernel-cache entry | `kernelcache.release.iphone14`, 21,518,572 bytes |
| Main `.aea`-suffixed images | `141-93752-026.dmg.aea` (8,455,716,864 bytes); `141-94047-030.dmg.aea` (2,415,919,104 bytes) |

Both `BuildIdentities` identify `iPhone14,5`/`d17ap`; the `OS` manifest path is `141-93752-026.dmg.aea` in both identities. These are archive claims, not a cryptographic Apple provenance check.

## Independent parser cross-check

The pinned Windows x86_64 `blacktop/ipsw` v3.1.743 `ipsw info --json` parser independently reported type `IPSW`, version `27.3`, build `24C5037i`, product `iPhone14,5`, board `D17AP`, and CPU identifier `t8110`. Version, build, product, and board agree with the locally parsed plist values (`d17ap` is case-insensitive equivalent to `D17AP`). This is an independent parser cross-check of the same local archive, not independent Apple provenance or signature verification.

Machine-readable output: `firmware/ipsw_parser_info.json`.

## Selected manifest digest cross-check

The script below was used to decode the BuildManifest `Digest` values (48-byte SHA-384) and stream the corresponding ZIP component bytes through SHA-384. This is a read-only consistency check, not Apple signature validation.

| Component | ZIP bytes | Manifest SHA-384 matches streamed entry? |
|---|---:|---|
| Erase ramdisk `141-93054-032.dmg` | 245,366,811 | Yes |
| Update ramdisk `141-92811-032.dmg` | 247,463,963 | Yes |
| Kernel cache `kernelcache.release.iphone14` | 21,518,572 | Yes |
| Cryptex AppOS `141-93238-032.dmg` | 18,874,368 | Yes |
| System AEA `141-93752-026.dmg.aea` | 8,455,716,864 | **No — unresolved** |
| Cryptex SystemOS AEA `141-94047-030.dmg.aea` | 2,415,919,104 | **No — unresolved** |

Observed SHA-384 for the two unmatched AEA entries:

- `141-93752-026.dmg.aea`: `5267ca24fb7b40a652594fee06f13d02d800c7e2db3e19854e11f65dca59a2207ecacde37455b5e76e848cdd2da8778b`
- `141-94047-030.dmg.aea`: `c3291e8286663a1c340b93191da375a2451a03b2ed450ca1655d7bf836243e25df3b6390d13c61a5ad70a96ca1d74510`

These are mismatches, not evidence that the file is corrupt or that the build manifest is wrong. The local IPSW was opened successfully, and several other manifest digests matched, but the digest scope/encoding for these AEA components has not been explained. Further validation must compare against a supported Apple/IMG4 implementation before drawing conclusions.

## Output files

- `ipsw_metadata.json` — plist-derived identity, build identities, and ZIP summary.
- `ipsw_inventory.csv` — all 113 entry names, compressed/uncompressed sizes, and SHA-256 digests.
- `ipsw_sha256.txt` — whole-file digest and input path.

## Provenance caveat

Apple's [Developer Releases page](https://developer.apple.com/news/releases/) lists **iOS 27.3 beta (24C5037i)** dated November 3, 2026. No other iOS 27.3 build appeared on the inspected release listing, so this is the latest iOS 27.3 beta entry visible there at review time. The page corroborates the release label and build number, but does not bind the exact local IPSW bytes or its iPhone14,5 manifest to an Apple-hosted checksum/signature. A separate public IPSW metadata lookup did not return this seed build.
