# Windows analysis toolchain

## Host

Recorded by `Initialize-WindowsResearch.ps1`: Windows 11 Pro x64, PowerShell `5.1.26100.9711`, AMD Ryzen 5 5600X (12 logical processors), 31.91 GiB RAM, 305.95 GiB free on the project drive.

The `python.exe` on PATH resolves to the Microsoft Store app alias and did not run as a Python interpreter. Python was not needed for the completed PowerShell workflow.

## IPSW parser

- Project: [`blacktop/ipsw`](https://github.com/blacktop/ipsw)
- Release: [v3.1.743](https://github.com/blacktop/ipsw/releases/tag/v3.1.743)
- Asset: `ipsw_3.1.743_windows_x86_64.zip`
- GitHub release asset SHA-256: `4633aee6d8f6edbe23c8fe946c8000700f7761e18f34c33a15779d9932165cf8`
- Installed executable: `tools/windows/bin/ipsw.exe`
- Executable SHA-256: `0ed57ff72353740fd6870033337b6a235d7036645a78034f10eea69433cc0c00`
- `ipsw.exe version`: `3.1.743`, build commit `b2c156960c79b0eefe0885dbb32cce7eef77cf2d`

The release archive checksum was read from the GitHub Releases API and verified before extracting the executable. This does not independently verify the publisher's identity or audit the binary. The binary and release ZIP are local-only and ignored by Git; the installer script is retained.

## Completed tool operations

- `ipsw extract --kernel` on the supplied IPSW, extracting only its kernel cache.
- `ipsw kernel version --json` for kernel version metadata.
- `ipsw kernel kexts --json` for the kernel extension metadata inventory.
- `ipsw macho info --header --loads` for the Mach-O header and load commands.

No commands were run to extract all kext binaries, decrypt AEA volumes, mount filesystems, or analyze vulnerabilities.

## Read-only APFS probes

The AppOS volume's sealed, headerless, hashed FS-tree was read by a purpose-built Rust walker using `apfs` 0.4.1 and `apfs-core` 0.2.6; an independent path walk was performed with `hadris-apfs` 3.0.0-rc.3 and its locked `hadris-io`/`hadris-storage` dependencies. Exact crate versions and reproducible commands are recorded in [tools/rust/README.md](../../../tools/rust/README.md). The Hadris package is a release candidate, and agreement between two reverse-engineered readers is corroboration rather than authoritative filesystem authentication.

The CLI help also exposes `ipsw fw aea --info`, IMG4 information commands, and IPSW mount subcommands. Help output confirms command availability only; AEA inspection, IMG4 inspection of other components, and mount behavior have not been validated on this Windows host. No private-key or key-lookup option was used.
