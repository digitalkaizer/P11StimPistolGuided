P-11 Guided Stim - Helldivers Only
Guidance Component Probe v0.10

READ-ONLY.

WHY v0.10
v0.9 found no live P-11/Warrant WeaponData records matching the serialized
layout/values we expected. Instead of continuing to chase weapon records,
v0.10 targets the part we actually need: Warrant-style missile guidance.

SEEKING MISSILE ANCHORS
- speed triplet: 90 / 90 / 90
- PID: 0.01 / 1 / 0.0001

Each candidate is scored using the known Warrant guidance shape:
- TargetLock mode
- ~0.01s guidance enable
- 180-degree guidance loss angle
- ~30s lifetime
- 90/90/90 speeds
- 200 acceleration
- 45 max target angle
- 30/30 turn speeds
- .01/1/.0001 PID
- zero target update interval
- surrounding -1/0 timing fields

It also logs simple SensorEye-like 60/-1/-1/20/20 patterns as a secondary
acquisition-system clue.

STRONG SUCCESS:
GUIDANCE_COMPONENT_CONFIRMED confidence=HIGH

NORMAL COMPLETION:
GUIDANCE_SCAN_COMPLETE

LOG:
%LOCALAPPDATA%/CowboyBingus/Helldivers2/Logs/P11GuidedStim.log

SAFETY:
ReadProcessMemory + VirtualQuery only.
No WriteProcessMemory.
No VirtualProtect.
No faction changes.
No Bastion changes.
No P-11 Self Heal changes.
