# BuildManifest component digest cross-check

## Method

- Input: the supplied iOS 27.3 IPSW and its `BuildManifest.plist`.
- Tool: `tools/windows/Test-BuildManifestDigests.ps1`, Windows PowerShell/.NET.
- The tool decoded each Base64 `Digest`, mapped 32-byte values to SHA-256 and 48-byte values to SHA-384, then compared that digest against the full bytes of the referenced ZIP entry.
- Results cover two BuildIdentities, 154 component-reference rows, and 69 unique path/algorithm hashes.

## Results

- 124 manifest reference rows matched the direct whole-entry digest.
- 30 rows did not match; the mismatches correspond to 15 unique component/path combinations repeated across the two build identities.
- All referenced paths were present in the archive.
- The kernel cache, both ramdisks, AppOS Cryptex image, and many firmware entries matched their manifest digests.
- Mismatches include the system and Cryptex SystemOS AEA images and several restore-specific entries whose component digest differs from the direct whole-entry digest.

## Interpretation and caveat

The `Digest` field cannot be assumed to mean “SHA-384 of the complete ZIP entry” for every manifest component role. The observed role-dependent mismatches demonstrate that a naïve whole-file comparison is not a complete manifest verifier. No mismatch is labeled corruption. Establishing the correct digest domain requires a format-aware implementation that understands IMG4, personalization, and manifest component semantics.

This check does not validate IMG4 signatures, AEA authentication, Apple provenance, or the correctness of component payloads. Treat `buildmanifest_digest_check.csv` as raw reproducible comparison evidence, not as a pass/fail authenticity verdict.
