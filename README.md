# P-11 Stim Pistol Guided — Research & Reverse-Engineering Handoff

Research repository by **DigitalKaizer** for a Helldivers 2 mod concept:

> Give the **P-11 Stim Pistol** a separate alternate fire mode using the **P-92 Warrant's lock-on / guided-shot behavior**, while keeping normal P-11 fire and the healing stim projectile intact.

This repository documents the work done so far, including the unsuccessful approaches. It is intentionally candid about dead ends so the next person does not have to spend time rediscovering them.

## A note on how this project was worked on

The reverse-engineering work in this repository was performed primarily with **standard ChatGPT**, not Codex.

I, **DigitalKaizer**, had run out of my available Work/Codex usage but still wanted to keep working on the idea, so I continued the investigation with standard ChatGPT and a series of read-only diagnostic addons built around Bingus Shared Loader.

During this process I noticed that **CowboyBingus also has essentially this same mod concept on his roadmap**. Rather than let the research sit unused, I am publishing the notes, probes, logs, IDs, hypotheses, and failures here and extending an open invitation to CowboyBingus—or anyone else working toward the same goal—to use anything here that is helpful.

If these notes save somebody time, a small attribution to **DigitalKaizer** for the research is welcome. It is not the main priority. The main priority is simply to get the mod made and make **Helldivers 2 more fun to play for everybody**.

**o7**

---

## Desired final behavior

### Normal fire
- Vanilla P-11 behavior remains unchanged.

### Guided alternate fire
- P-92 Warrant-style lock-on / target acquisition if practical.
- Lock onto **other Helldivers only**.
- Fire a homing **stim dart**, not a damaging Warrant projectile.

### Target restrictions
Allowed:
- another player-controlled Helldiver.

Rejected:
- shooter/self,
- enemies,
- SEAF troops in the base version,
- arbitrary neutral/friendly factions.

### Projectile restrictions
- Preserve the P-11 heal/stim projectile path.
- Preserve `ProjectileType_Pistol_Stim` behavior/effect.
- Do **not** simply replace it with `ProjectileType_Pistol_Warrant`.

### Scope / compatibility
- Guided Stim is a **separate mod** from P-11 Self Heal.
- Compatibility with P-11 Self Heal is desirable, but merging them is not the goal.
- Approximate desired lock range: **~60 m**.
- Avoid global faction spoofing or global faction edits.
- Avoid unrelated mod dependencies.
- Bingus Shared Loader is an acceptable shared-loader dependency if runtime code is required.

## Current status

There is **no working prototype yet**.

The useful product of the investigation is therefore:

1. a fairly complete list of approaches that **did not work**;
2. corrected findings after we discovered self-match contamination in early probes;
3. P-11 / P-92 resource IDs and relevant component names;
4. source for each diagnostic probe in the local research archive, with selected probe metadata preserved here;
5. selected raw logs plus hashes/provenance for larger omitted logs;
6. an explicit warning about the final unsafe `MEM_IMAGE` experiment;
7. a suggested next direction that avoids repeating the same memory-scanning approach.

## Most important lesson

The broad "scan several gigabytes of the process for serialized DataLibrary structs" approach has been exhausted.

Early runs were contaminated because the Lua addon contained the same constants it searched for. Later runs correctly excluded the addon's own allocation, but still failed to recover coherent serialized `ProjectileWeapon`, `WeaponData`, `SeekingMissile`, or `SensorEye` records from `MEM_PRIVATE`.

The last experiment broadened the search to `MEM_MAPPED` and `MEM_IMAGE`:

- `MEM_MAPPED` produced **zero** coherent P-11/Warrant/DataLibrary signals.
- The `MEM_IMAGE` fallback produced white flickering and CTDs and is **abandoned**.

Do **not** continue this project by simply adding more signatures to the same scanner.

## Recommended next directions

A contributor with deeper Stingray / loader knowledge should preferably attack this from the resource or execution side instead:

1. Trace how current `generated_entities.dl_bin` data is decoded/materialized by the game.
2. Observe or hook the resource-loader boundary rather than scanning multi-gigabyte address spaces.
3. Trace the P-92 Warrant entity/component graph into:
   - weapon functions,
   - lock acquisition / targeting,
   - `SensorEyeComponent`,
   - `SeekingMissileComponent`,
   - `GuidanceTargetComponent`, if involved.
4. Identify the minimum path that creates Warrant-style lock acquisition and target assignment.
5. Reproduce that path on a P-11 alternate mode while preserving the P-11 stim projectile.
6. If cloning the Warrant projectile path is messy, use the Warrant-like lock acquisition only, then steer/retarget the normal stim dart toward the selected Helldiver.

## Repository map

- [`docs/CURRENT_GOAL.md`](docs/CURRENT_GOAL.md) — exact intended final behavior.
- [`docs/RESEARCH_FINDINGS.md`](docs/RESEARCH_FINDINGS.md) — confirmed, supported, and hypothesis-level findings.
- [`docs/PROBE_HISTORY.md`](docs/PROBE_HISTORY.md) — every diagnostic version and what it taught us.
- [`docs/TECHNICAL_NOTES.md`](docs/TECHNICAL_NOTES.md) — low-level IDs, offsets, historical/current distinctions, and implementation notes.
- [`docs/COWBOYBINGUS_HANDOFF.md`](docs/COWBOYBINGUS_HANDOFF.md) — condensed technical handoff for CowboyBingus.
- [`docs/SAFETY.md`](docs/SAFETY.md) — things not to repeat.
- [`references/KNOWN_IDS.md`](references/KNOWN_IDS.md) — known hashes/resource IDs.
- [`references/EXTERNAL_SOURCES.md`](references/EXTERNAL_SOURCES.md) — FileDiver, HelldiversData, Bingus Shared Loader references.
- [`references/FILE_HASHES.md`](references/FILE_HASHES.md) — hashes of local reference files not redistributed here.
- [`probes/`](probes/) — selected probe packaging metadata/notes and archive warnings. Full probe behavior/results are documented in `docs/PROBE_HISTORY.md` and `docs/TECHNICAL_NOTES.md`.
- [`unsafe/v0.11-UNSAFE/`](unsafe/v0.11-UNSAFE/) — forensic notes for the final crash-associated experiment; **do not rerun it as described**.
- [`logs/`](logs/) — selected preserved diagnostic logs plus hashes/provenance for larger omitted raw logs.

## Evidence labels

Throughout the documentation:

- **CONFIRMED** — directly demonstrated by a discriminating test or artifact.
- **SUPPORTED** — evidence points this way, but does not uniquely prove it.
- **LEADING HYPOTHESIS** — plausible working explanation awaiting a discriminating test.

## Important caveat about v0.1-v0.4

The first probes contained the same byte signatures they searched for. They therefore matched their own LuaJIT/module allocation and produced convincing-looking but invalid addresses.

v0.5 introduced full `AllocationBase` self-exclusion and invalidated the earlier live-address conclusions.

Do not reuse v0.1-v0.4 addresses as game structures.

## Collaboration / reuse

CowboyBingus is specifically invited to use these notes, probe ideas, IDs, logs, or source in whatever way is useful toward achieving the common mod goal.

Other Helldivers 2 modders are welcome to study the work and contribute findings as well. If this research materially helps a released mod, attribution to **DigitalKaizer** is appreciated, but getting a good mod into players' hands matters more.

No formal open-source license has been selected yet; see [`NO_LICENSE_SELECTED.md`](NO_LICENSE_SELECTED.md).
