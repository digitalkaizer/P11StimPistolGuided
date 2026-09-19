# Probe History

| Version | Purpose | Result | Keep? |
|---|---|---|---|
| v0.1 | Basic UID scan | Logger bug truncated output | Historical only |
| v0.1.1 | Fixed cumulative logging | Found many apparent P-11/Warrant hits | **Contaminated by self-match** |
| v0.2 | Component-map probing | Built on signature scanning | Historical only |
| v0.3 | Structural-map probing | Built on signature scanning | Historical only |
| v0.4 | Raw anchor map | Produced huge apparent map/anchor set | **Invalidated by self-match** |
| v0.5 | Self-excluding DataLibrary probe | Self-exclusion worked; no serialized DL blob resolved | Useful negative |
| v0.6 | Direct resource-map pair probe | Hashes found, but same-index pairs disproved PW map assumption | Useful negative |
| v0.7 | Direct ProjectileWeapon record scan | 0 P-11; 4 Warrant-zeroing-like candidates; validation failed | Useful negative |
| v0.8 | Warrant layout inference | Scan completed; game crashed before post-scan analysis | Do not prefer |
| v0.8.1 | Inline safe context capture | Captured 4 identical blobs; completed cleanly | Useful evidence |
| v0.9 | Direct WeaponData scan | 0 P-11 / 0 Warrant | Useful negative |
| v0.10 | Direct SeekingMissile/SensorEye scan | 0 guidance / 0 sensor patterns | Useful negative |
| v0.11 | MEM_MAPPED then MEM_IMAGE locator | MEM_MAPPED empty; white flicker + CTD after MEM_IMAGE fallback | **UNSAFE / abandoned** |

## Important interpretation

This sequence should **not** be read as "the Warrant has no lock components." It shows that the attempted serialized fingerprints were not discoverable using these broad in-process memory scans.

The next investigation should change techniques, not add another signature to the same scanner.
