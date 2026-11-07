# dyld shared cache report — iOS 27.3

## Attempted extraction

Tool: Windows x86_64 `blacktop/ipsw` v3.1.743. The command attempted to extract the `arm64e` dyld shared cache from the local iPhone14,5 IPSW without `--lookup`, key input, or decryption options:

```powershell
ipsw.exe extract --dyld --dyld-arch arm64e --device iPhone14,5 --output <local-output> <target.ipsw>
```

The tool exited with code 1 and reported: `dyld extraction is not supported on Windows (see github.com/blacktop/go-apfs)`. No dyld cache was produced or analyzed. Transcript: `research/ios-27.3/logs/dyld-extraction-attempt.txt`.

## Status and limits

- No dyld cache, shared-cache image list, framework inventory, or binary diff is available for 27.3.
- The tool explicitly identifies a Windows platform limitation; no alternate parser was installed or validated.
- The cache may reside in an AEA-protected volume, but this attempt did not establish its exact container path or decryption state.
- The saved 27.2 reports are not a substitute for a 27.3 extraction.

Continue only if a documented Windows-native parser can be validated without key acquisition or decryption.
