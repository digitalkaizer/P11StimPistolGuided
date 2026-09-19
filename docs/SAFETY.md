# Safety / Do-Not-Repeat Notes

## Do not trust v0.1-v0.4 addresses
Those probes self-matched their own LuaJIT allocation.

## Do not run v0.11 as-is
The source is retained only so another developer can see exactly what was tested.

The tester observed:
- white screen/flickering,
- CTDs,
after the probe transitioned from `MEM_MAPPED` to broad `MEM_IMAGE` scanning.

The log did not reach its normal completion marker.

## Do not make global faction changes
The requested target restriction is local to the guided-stim mechanic:
- other Helldivers only.

Global faction edits risk interacting with unrelated vehicle aggro work.

## Do not merge Guided Stim into P-11 Self Heal
They are separate mods by design.
