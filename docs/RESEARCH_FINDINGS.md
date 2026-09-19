# Research Findings

Labels used here:

- **CONFIRMED** — directly demonstrated by a discriminating test or exact artifact.
- **SUPPORTED** — evidence points this way, but not uniquely.
- **LEADING HYPOTHESIS** — plausible working model, not yet demonstrated.

## CONFIRMED

### Self-match contamination invalidated v0.1-v0.4 live-memory conclusions
Early probes embedded the same byte signatures they searched for. Large portions of the reported P-11/Warrant hits came from the LuaJIT addon allocation itself.

Evidence included P-11 UID bytes sitting next to literal diagnostic strings such as `WARRANT_UID`.

Therefore:
- Do not reuse addresses from v0.1-v0.4.
- Do not cite those addresses as game component tables.
- Offline historical extraction from older builds is still usable as historical evidence, but not as a live address map.

### v0.5 self-exclusion works
v0.5:
1. embeds a unique marker,
2. finds that marker,
3. resolves its `AllocationBase`,
4. excludes the entire allocation from later scans.

This corrected the self-match class.

### Intact serialized DataLibrary blobs were not found in MEM_PRIVATE
v0.5 scanned large private allocations looking for `DLInstanceHeader` + the current `ProjectileWeaponComponentData` type hash and did not structurally resolve the expected blob.

### Serialized `ComponentIndexData` map assumptions failed
v0.6 found P-11/Warrant resource-hash occurrences outside our Lua allocation, but the only same-allocation candidate pairs gave both weapons the same component index (`3/3` and `0/0`). They were not the projectile-weapon map.

### Direct old/current serialized ProjectileWeapon fingerprints were absent from MEM_PRIVATE
v0.7 searched for the old/current P-11/Warrant serialized record fingerprints. It found four Warrant-zeroing-like blobs but no P-11 record.

### The four `75 / 150 / 300` blobs are identical copies and are not a trustworthy Warrant record
v0.8.1 captured four raw contexts inline and completed without post-scan analysis. All four contexts are byte-for-byte identical across separate allocations.

The bytes immediately preceding `75/150/300` decode as `400/600/800`, which does not fit the expected Warrant fire-rate model. Treat this as a repeated unrelated settings blob unless independently proven otherwise.

### Direct WeaponData fingerprints were absent from MEM_PRIVATE
v0.9 searched current P-11/Warrant handling signatures in the serialized `WeaponDataComponent` layout and found neither.

### Old Warrant SeekingMissile / SensorEye fingerprints were absent from MEM_PRIVATE
v0.10 found zero guidance candidates and zero SensorEye-like patterns.

### MEM_MAPPED produced no relevant signals
v0.11 scanned ~2.19 GB of readable `MEM_MAPPED` regions and found:
- P-11 UID: 0
- Warrant UID: 0
- P-11 ProjectileWeapon signature: 0
- P-11 WeaponData signature: 0
- ProjectileWeaponComponentData hash: 0

### MEM_IMAGE fallback is abandoned
Immediately after v0.11 switched from `MEM_MAPPED` to `MEM_IMAGE`, the tester observed white flickering and CTDs.

The only logged image hit was an uncorroborated `0x45171B68` occurrence. No P-11/Warrant UID or weapon signature corroborated it.

**Do not repeat broad in-process MEM_IMAGE scanning.**

The CTD's exact cause is not proven, but the timing makes this path not worth continuing.

## SUPPORTED

### Runtime representation is not the same thing as serialized DataLibrary layout
Repeated failures to find exact serialized records, despite independently extracted weapon values, strongly suggest that relevant runtime objects are transformed, decomposed, copied selectively, referenced indirectly, or not resident in the scanned memory classes in serialized form.

### Resource-loader / decode interception is a better next path than heuristic scanning
The raw `generated_entities.dl_bin` from the current install is high-entropy/packed on disk, while public tooling successfully produces decoded component JSON. Following the game's own decode/load path is therefore more promising than scanning multi-gigabyte memory for serialized records.

## LEADING HYPOTHESES / OPEN QUESTIONS

1. Warrant lock acquisition likely spans more than `ProjectileWeaponComponent`.
2. `WeaponData` weapon functions, `SensorEye`, `SeekingMissile`, and possibly `GuidanceTargetComponent` are likely involved.
3. The final mod may need:
   - Warrant-style lock acquisition/UI,
   - a target eligibility filter limited to other players,
   - then either a P-11-specific guided entity or runtime steering of the existing stim projectile.
4. Warrant `ProjectileEntity` cannot simply replace the P-11 projectile because that would likely produce the Warrant damage projectile instead of a healing dart.
5. Global faction changes are explicitly out of scope.
