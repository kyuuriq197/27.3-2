# Security report — scope and result

## Scope

This is not a vulnerability assessment. The work performed was limited to archive metadata, component inventory, and hashes. No vulnerability hunting, exploitability testing, exploit chain, or jailbreak analysis was performed.

## Findings

- No vulnerability finding was established in this pass.
- This is **not** evidence that the IPSW is free of vulnerabilities.
- No Apple signature/provenance validation was performed for the supplied archive.
- The public IPSW discovery endpoint queried during the run did not return `iPhone14,5 / 27.3 / 24C5037i`. The embedded metadata is verified locally, but the latest-seed status remains unresolved.
- Apple's public release/advisory page is [Apple Security Releases](https://support.apple.com/en-us/100100). Public production-release advisories do not establish the security status of an unlisted developer seed.

## Responsible research note

Any future security study should remain authorized, non-invasive, and evidence-based. Report only validated observations through Apple's security reporting process; do not infer a vulnerability from a changed file hash.
