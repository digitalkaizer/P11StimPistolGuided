# CowboyBingus Handoff

Hey CowboyBingus — DigitalKaizer here.

I noticed you also have essentially this same concept on your roadmap: a guided / lock-on version of the P-11 Stim Pistol. I had already been trying to reverse-engineer it from my side, so rather than let the work disappear into a chat history I packaged all of it here.

This project was worked on primarily with **standard ChatGPT, not Codex**. I was out of my available Work/Codex usage but wanted to keep working anyway, so I kept pushing on it with standard ChatGPT and Bingus Shared Loader diagnostic addons.

Unfortunately we did not get as far as a working prototype. What we *did* get is a fairly good map of approaches that appear to be dead ends, plus useful IDs, component names, logs, and corrected findings.

You are openly invited to use any of this that is helpful toward making the mod. If it saves you time, a small attribution for the research is welcome, but that is secondary. The main priority is getting the feature working and making Helldivers 2 more fun to play for everyone. o7

## Exact goal

- P-11 normal fire remains vanilla.
- Alternate mode gives a P-92 Warrant-like lock-on experience.
- Lock target = **another Helldiver only**.
- Reject self, enemies, and SEAF troops in the base version.
- Preserve the P-11 stim/heal projectile and effect.
- Do **not** substitute the Warrant's damage projectile.
- Keep the mod separate from P-11 Self Heal.
- Do not solve target eligibility through global faction mutation/spoofing.

## The most important failure

Early probes were invalid because the addon literally embedded the signatures it searched for and matched itself.

v0.5 fixed that by finding a unique marker, resolving its entire `AllocationBase`, and excluding that allocation before scanning.

After the self-match fix:

- serialized DataLibrary header search did not resolve the expected tables;
- serialized `ComponentIndexData` reconstruction failed;
- direct `ProjectileWeaponComponent` fingerprints failed;
- direct `WeaponDataComponent` fingerprints failed;
- old Warrant `SeekingMissile` / `SensorEye` fingerprints failed;
- `MEM_MAPPED` contained none of the P-11/Warrant control signatures;
- broad `MEM_IMAGE` scanning caused white flickering / CTDs and is abandoned.

Please do **not** continue the multi-gigabyte signature-scan strategy just by trying more constants.

## Where I think your loader/Stingray experience can shortcut this

The likely productive route is at the game's resource loading / decoded component boundary.

1. Find where current `generated_entities.dl_bin` is decoded/materialized.
2. Observe or hook that stage rather than trying to rediscover the serialized structures afterward.
3. Resolve the Warrant's current component graph.
4. Follow its lock acquisition through any relevant:
   - `WeaponDataComponent`
   - weapon-function structures
   - `SensorEyeComponent`
   - `SeekingMissileComponent`
   - `GuidanceTargetComponent`
5. Identify the minimum data/code path that creates the lock UI and assigns a target.
6. Apply only that part to a P-11 alternate function.
7. Keep the projectile/effect on the stim side.

If the Warrant's spawned projectile entity is inseparable from its damage path, an alternate implementation would still satisfy the gameplay goal:

- reproduce Warrant-style lock acquisition,
- retain the normal P-11 stim dart,
- steer/retarget the stim projectile toward the locked Helldiver.

## Key IDs

- P-11 model/resource: `0xD6B1FB05B9109353`
- P-92 Warrant model/resource: `0xCF8934FF6567A42D`
- P-92 Warrant projectile entity: `0x8053DA4358971392`
- P-11 package: `0x2844DA91583DB0F4`
- Warrant package: `0x5D5C93EF05A46880`

See `references/KNOWN_IDS.md` for the complete list and context.

## Public data sources worth using

`shalzuth/HelldiversData` / `data.helldivers.io` exposes decoded component JSON and was more useful late in this project than continuing to assume FileDiver's embedded generated data matched the current September build exactly.

Relevant paths and links are in `references/EXTERNAL_SOURCES.md`.

## Crash warning

`unsafe/v0.11-UNSAFE/` is retained only for forensic reference.

It scanned `MEM_MAPPED` first with no useful hits, then moved to `MEM_IMAGE`. During the image scan the game began white flickering and CTDing.

Do not run it as-is.
