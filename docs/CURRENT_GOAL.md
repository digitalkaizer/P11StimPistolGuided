# Current Goal

## Base mod

Create an independent mod for the P-11 Stim Pistol.

### Normal mode
Vanilla P-11 behavior.

### Guided alternate mode
Use the P-92 Warrant's lock-on feel/mechanics to select another Helldiver and launch a homing stim dart.

### Target policy
Allowed:
- another player-controlled Helldiver.

Disallowed:
- self/shooter,
- enemies,
- SEAF NPCs in the base release,
- arbitrary neutral/friendly factions.

### Projectile policy
The final projectile/effect must remain the P-11 stim/heal projectile path.

Do not solve this by replacing the dart with `ProjectileType_Pistol_Warrant`.

### Scope / compatibility
- Separate from P-11 Self Heal.
- Self Heal compatibility is a bonus.
- No dependency on Bastion Tank Lunchbox Fix.
- No global faction mutation/spoofing.
- Avoid bootstrapping unrelated mods. If a shared loader is absolutely necessary, Bingus Shared Loader is the existing relevant loader.

### Nice-to-have
- Warrant-style lock reticle.
- ~60 m lock range.
- Future optional module could allow SEAF troops, but **not in the base version**.
