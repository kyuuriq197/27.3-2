# AEA metadata report — iOS 27.3

## Inputs and verification

The two AEA entries were streamed from the supplied IPSW to ignored local storage using `tools/windows/Run-AeaMetadata.ps1`. Their lengths and SHA-256 values match the saved ZIP inventory.

| Manifest role | Entry | Bytes | SHA-256 |
|---|---|---:|---|
| SystemOS | `141-93752-026.dmg.aea` | 8,455,716,864 | `140649c4465be3c606f083d58ba85d7f2d9b8f6d80f3ebdbd90bffcfba7f73e6` |
| Cryptex SystemOS | `141-94047-030.dmg.aea` | 2,415,919,104 | `c3bfebcb86b324748160b33559d8a5efc40955abc00ad842b2116409f82f83e1` |

Tool: Windows x86_64 `blacktop/ipsw` v3.1.743. The AEA info parser succeeded for both files. It reported these metadata field names:

- `com.apple.wkms.fcs-key-url`
- `com.apple.wkms.url`
- `com.apple.wkms.auth-data`
- `com.apple.wkms.fcs-response`

Only the field names, container lengths, and hashes are persisted in `aea_metadata_summary.json`. Values from the parser output, including authentication data and wrapped-response data, are deliberately not written to reports or logs.

## Scope and limits

- No FCS-key retrieval, private-key operation, decryption, or filesystem mount was performed.
- Parsing the AEA information fields does not validate Apple provenance, establish that a key is available, or prove that either volume can be decrypted.
- The linked [iOS 27.2 analysis](https://github.com/kyuuriq197/iphone14-5-ios-27-2-ipsw-analysis) reports successful FCS-key retrieval and offline decryption for build `24B5099f`. That is evidence about the cited 27.2 build only; availability or behavior for this 27.3 seed build was not tested. No authorization requirement is inferred here.
- This does not inventory APFS, Cryptex files, dyld caches, or system binaries.
- Existing BuildManifest digest mismatches for the two AEA entries remain unresolved; successful AEA metadata parsing does not explain those mismatches or authenticate the files.

## Evidence files

- `research/ios-27.3/aea/aea_metadata_summary.json`
- `research/ios-27.3/logs/aea-metadata-run.txt`
- The large AEA files are local-only under `research/ios-27.3/data/aea/`.
