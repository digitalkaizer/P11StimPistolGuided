# Technical Reverse-Engineering Notes

This file collects the low-level details that were spread across the working conversation and probe source. It is intentionally explicit about which data is **current**, **historical**, or merely a **hypothesis**.

## Project invariants

The intended mod must preserve these constraints:

- P-11 normal fire remains vanilla.
- Guided fire is a separate alternate function/mode.
- Guided target eligibility is local to the mechanic: **other Helldivers only**.
- Self/shooter, enemies, and SEAF troops are rejected in the base version.
- The final projectile/effect remains the P-11 stim/heal path.
- Do not solve this by firing the P-92 Warrant damage projectile.
- Do not make global faction changes.
- Guided Stim remains separate from the existing P-11 Self Heal mod.

## Known resource IDs

### P-11

- Decimal: `15470422196992643923`
- Hex: `0xD6B1FB05B9109353`
- Little endian: `53 93 10 B9 05 FB B1 D6`
- Package: `0x2844DA91583DB0F4`

### P-92 Warrant

- Decimal: `14954542309183038509`
- Hex: `0xCF8934FF6567A42D`
- Little endian: `2D A4 67 65 FF 34 89 CF`
- Package: `0x5D5C93EF05A46880`

### Warrant projectile entity

- Decimal: `9246974442705720210`
- Hex: `0x8053DA4358971392`
- Little endian: `92 13 97 58 43 DA 53 80`

Community names encountered:

- `DamageInfoType_Projectile_Pistol_Warrant`
- `ProjectileType_Pistol_Warrant`
- `ProjectileType_Pistol_Warrant_MK2`
- `ProjectileType_Pistol_Stim`

## P-11 Self Heal reference

This is **compatibility/reference material only**. Guided Stim must not own or import the Self Heal module.

Reference build:

- Name: `P-11 Self Heal - Wild Bridge v8`
- GUID: `06ee0a4e-aa52-401a-a814-61cd1e0bf14c`
- Module: `mods/codex/p11_self_heal`
- Revision: `v8-wild-bridge`
- Bingus/Codex Module Bridge-era loader dependency in that build.
- Reference ZIP SHA-256: `8c7b1740064eeb349e015ab67acb9a1d1b559226301f7d97c78284032906faf6`

Known runtime/reference values from that project:

- native call RVA: `0xF298A0`
- effects: `[28,26,25]`
- shot manager RVA: `0x276C370`
- map offset: `0x18`
- record stride: `0xE0`
- cycle offset: `0x08`
- active offset: `0x10`
- flag offset: `0x14`
- six-round magazine / 24 reserve in the referenced build
- archive resource hash: `0xB62FABC479E8EDA1`
- archive type: `0xA14E8DFA2CD117E2`
- archive offset: `0xC0`
- patch size: `23104`

These values are not assumed to solve Guided Stim; they are retained to help avoid compatibility collisions.

## FileDiver-era current ProjectileWeapon layout

The current FileDiver source inspected during this project described `ProjectileWeaponComponent` with a serialized record size of **`0x180`**.

Relevant offsets used by our probes:

| Offset | Field |
|---:|---|
| `0x000` | projectile type |
| `0x004` | rounds-per-minute `Vec3` |
| `0x010` | zeroing slots `Vec3` |
| `0x01C` | zeroing hipfire |
| `0x020` | current/default zeroing slot |
| `0x024` | infinite ammo bool |
| `0x028` | `ProjectileEntity` Stingray hash |
| `0x160` | burst fire rate |
| `0x164` | weapon-function muzzle velocity |
| `0x168` | weapon-function projectile type |
| `0x16C` | unknown particle/effect hash |
| `0x174` | unknown bool |

FileDiver's comment on `ProjectileEntity` was important: when set, an entity is spawned when firing instead of simply adding a projectile to the projectile manager.

That makes the Warrant `ProjectileEntity` path a likely reason why simply copying the Warrant's projectile-weapon configuration would be wrong for this mod: it risks spawning the Warrant damage entity instead of a stim dart.

### FileDiver-era component data geometry

At the time inspected:

- `ComponentIndexData hashmap[436]`
- `ProjectileWeaponComponent data[219]`
- `ComponentIndexData` serialized as `u64 resource_id; u32 index; u32 padding`
- map bytes: `436 * 16 = 0x1B40`
- records: `219 * 0x180 = 0x14880`
- expected payload: `0x163C0`
- `ProjectileWeaponComponentData` DL hash: `0x45171B68`

**Important:** v0.5/v0.6 did not structurally recover this serialized arrangement from the live September process. Treat this as parser/file-format knowledge, not a proven runtime-memory layout.

## Weapon-function hypothesis

An older `WeaponDataComponent` extraction suggested the Warrant may involve a programmable-ammo style weapon function (`ProgrammableAmmo=8` was considered).

This was **never confirmed** as the actual Warrant alt-lock mechanism and should remain a hypothesis only.

## Historical Warrant SeekingMissile profile

The following profile came from an older July 2026 extraction and was used to design some probes. It is **historical evidence, not guaranteed current September data**.

Historical Warrant-like values:

- targeting mode: `2` / TargetLock
- unknown/targeting float: about `0.016666`
- autoplay starting effects: false
- guidance enable: about `0.01`
- guidance loss angle: `180`
- movement prediction accuracy: `0`
- max lifetime: `30`
- starting speed: `90`
- minimum speed: `90`
- preferred speed: `90`
- acceleration: `200`
- max angle to target: `45`
- minimum turn speed: `30`
- maximum turn speed: `30`
- PID used during probing: approximately `0.01 / 1 / 0.0001`
- movement processed by projectile system: true
- projectile type to process: none / `0`

### Why v0.10 should not be over-interpreted

Later public `HelldiversData` extraction showed that current `SeekingMissileComponentData` contains many profiles whose values differ substantially from the old July assumptions. Therefore v0.10's failure to find the exact old `90/90/90 + PID` fingerprint does **not** demonstrate that Warrant guidance no longer exists.

It only demonstrates that the old historical fingerprint was not resident in scanned `MEM_PRIVATE` regions in that exact form.

## Historical Warrant SensorEye profile

Older extraction suggested a Warrant-associated SensorEye profile approximately like:

- distance: `60`
- peripheral/back values: `-1 / -1`
- horizontal angle: `20`
- vertical angle: `20`
- sight node: `2948928094`
- raycast template: `13`
- eye type: Humanoid

Again: **historical**, and not confirmed as the September runtime representation.

## Current `SeekingMissileComponent` field shape from FileDiver source

The current source inspected includes fields for:

- targeting mode
- targeting/timing float
- autoplay effects
- time to enable/disable guidance
- distance/unknown guidance-enable and disable values
- angle lost guidance
- movement prediction accuracy
- five additional floats
- time to enable movement
- time to enable explosive
- max lifetime
- starting / minimum / preferred speeds
- acceleration
- max angle to target
- min/max turn speed
- P/I/D factors
- target update interval
- missile trail effect/audio
- target-dot minimum
- javelin mode
- whether the projectile system processes movement
- projectile type to process
- deviation settings
- trailing unknown fields/hashes/bools

This component still looks like a plausible part of the Warrant guidance path, but we never resolved the specific current Warrant record from the live process.

## `generated_entities.dl_bin`

Current-install reference materialized during this project:

- size: `45,630,838` bytes
- SHA-256: `1607b1153052bb3caea54e2c1e35a9295ec9f39b795d3bef61d83578c5fb313b`

The raw file appeared high-entropy/packed and did not expose the known entity IDs or DataLibrary structures as straightforward plaintext/serialized records.

This strongly motivated the recommendation to follow the game's decode/resource-load path rather than search the raw file manually.

## Public extraction sources

Two public sources were important:

### FileDiver

`xypwn/filediver`

Useful for:

- component struct definitions
- parser geometry
- comments on uncertain fields
- historical/current source changes

Caution: the public history inspected included an August 12, 2026 `01.007.000` update, so its embedded generated data was not blindly treated as identical to the later September install.

### HelldiversData / data.helldivers.io

`shalzuth/HelldiversData`

This exposes already-decoded JSON such as:

- `ProjectileWeaponComponentData.json`
- `WeaponDataComponentData.json`
- `SeekingMissileComponentData.json`
- `SensorEyeComponentData.json`
- `GuidanceTargetComponentData.json`
- `EntityComponentMap.json`

Late in the investigation this became a better source for current extracted values than continuing to infer them from live-memory fingerprints.

## Probe-methodology correction: self matching

The most important methodology correction happened after v0.4.

### What went wrong

The probe Lua source itself contained constants such as:

- P-11 UID bytes
- Warrant UID bytes
- zeroing/RPM signatures
- diagnostic string names describing those signatures

The scanner walked committed private memory and found those constants inside the LuaJIT/module allocation. This produced convincing-looking "live" hits that were actually the probe finding itself.

### Fix in v0.5

v0.5:

1. embedded a unique self marker;
2. searched for the marker;
3. used `VirtualQuery` to resolve the marker's `AllocationBase`;
4. excluded the **entire allocation** from later scans.

Any v0.1-v0.4 live-memory address should therefore be treated as retracted unless independently reproduced after self-exclusion.

## Probe results after self-exclusion

### v0.5 — serialized DataLibrary-header approach

- self-exclusion succeeded;
- scanned approximately 8.58 GB across selected large private regions;
- did not resolve the expected `ProjectileWeaponComponentData` header/payload;
- no writes.

### v0.6 — resource-map pair approach

- real P-11/Warrant hash occurrences existed outside the Lua allocation;
- same-allocation candidate pairs resolved both P-11 and Warrant to identical component indices (`3/3` and `0/0`);
- therefore they could not be the distinct ProjectileWeapon records we wanted;
- serialized flat map assumption rejected.

### v0.7 — direct ProjectileWeapon-record approach

- no validated P-11 record;
- four repeated `75/150/300` zeroing-like candidates;
- those candidates failed the expected record validation.

### v0.8 / v0.8.1 — context capture

v0.8 finished scanning but crashed before its separate post-scan analysis.

v0.8.1 removed post-scan memory reads and captured the four contexts inline while the chunks were already available.

Result:

- all four contexts were byte-for-byte identical;
- preceding values decoded as `400/600/800`;
- no supporting Warrant projectile-entity hash was found;
- treat this as a repeated unrelated settings/data blob, not a confirmed Warrant record.

### v0.9 — WeaponData fingerprints

- no validated P-11 WeaponData record;
- no validated Warrant WeaponData record;
- serialized live-record fingerprint approach rejected for scanned private memory.

### v0.10 — historical SeekingMissile / SensorEye fingerprints

- no guidance candidates;
- no SensorEye pattern hits;
- historical fingerprint not present in scanned private memory.

### v0.11 — mapped/image experiment

`MEM_MAPPED` scan:

- P-11 UID: 0
- Warrant UID: 0
- P-11 ProjectileWeapon signature: 0
- P-11 WeaponData signature: 0
- ProjectileWeaponData hash: 0

The probe then switched to broad `MEM_IMAGE` scanning.

Observed during that phase:

- white visual flickering;
- CTDs.

The log captured one isolated `0x45171B68` occurrence in an image page, but it had no corroborating P-11/Warrant resource or record signals.

The image hit is therefore **not accepted as a DataLibrary discovery**.

Broad in-process `MEM_IMAGE` scanning is abandoned.

## Best next technical direction

The next contributor should change techniques rather than increase scan complexity.

Recommended order:

1. Trace `generated_entities.dl_bin` resource decode/load in `game.dll` or existing Stingray/Bingus loader hooks.
2. Observe the decoded component data at a known loader boundary.
3. Resolve the current Warrant component graph directly.
4. Identify the mechanism that produces lock acquisition/reticle and target assignment.
5. Reuse only that lock/target portion for the P-11 alternate mode.
6. Preserve the stim projectile/effect.
7. If the Warrant projectile entity cannot be separated from damage behavior, implement guidance by steering/retargeting the normal stim projectile after lock acquisition.

That would achieve the gameplay goal without requiring the P-11 to become a Warrant internally.
