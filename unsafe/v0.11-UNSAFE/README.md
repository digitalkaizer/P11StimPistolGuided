# v0.11 — UNSAFE / forensic notes only

Do **not** rerun this experiment as originally designed.

v0.11 was the final attempt to determine whether the serialized DataLibrary control signatures were resident outside `MEM_PRIVATE`.

## What it did

1. Enumerated readable committed `MEM_MAPPED` regions.
2. Searched for:
   - P-11 resource ID `0xD6B1FB05B9109353`
   - Warrant resource ID `0xCF8934FF6567A42D`
   - current P-11 ProjectileWeapon signature
   - current P-11 WeaponData signature
   - `ProjectileWeaponComponentData` hash `0x45171B68`
3. If mapped memory failed, it fell back to broad `MEM_IMAGE` scanning.

## Result

`MEM_MAPPED` completed with zero relevant signals:

- P-11 UID: 0
- Warrant UID: 0
- P-11 ProjectileWeapon: 0
- P-11 WeaponData: 0
- ProjectileWeapon DataLibrary hash: 0

After the probe transitioned to `MEM_IMAGE`, the tester observed **white visual flickering and CTDs**.

The incomplete log captured a single `0x45171B68` occurrence in an image page. It had no corroborating P-11/Warrant UID or serialized record signals and is **not accepted as a DataLibrary discovery**.

## Conclusion

Broad in-process `MEM_IMAGE` scanning is abandoned.

The next approach should target the resource decode/load path or a known Stingray/Bingus loader boundary rather than scanning executable image pages.

See:

- `logs/v0.11-UNSAFE-crash-session.log`
- `docs/TECHNICAL_NOTES.md`
- `docs/RESEARCH_FINDINGS.md`
