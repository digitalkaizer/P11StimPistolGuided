# Known IDs / Hashes

## Weapon/model resources

| Item | Decimal | Hex | Little-endian bytes |
|---|---:|---|---|
| P-11 model/resource | 15470422196992643923 | `0xD6B1FB05B9109353` | `53 93 10 B9 05 FB B1 D6` |
| P-92 Warrant model/resource | 14954542309183038509 | `0xCF8934FF6567A42D` | `2D A4 67 65 FF 34 89 CF` |
| P-92 Warrant projectile entity | 9246974442705720210 | `0x8053DA4358971392` | `92 13 97 58 43 DA 53 80` |

## Packages

| Item | Decimal | Hex |
|---|---:|---|
| P-92 Warrant package | 6727414598185805952 | `0x5D5C93EF05A46880` |
| P-11 package | 2901684377718993140 | `0x2844DA91583DB0F4` |

## Community enum/name strings encountered

- `DamageInfoType_Projectile_Pistol_Warrant`
- `ProjectileType_Pistol_Warrant`
- `ProjectileType_Pistol_Warrant_MK2`
- `ProjectileType_Pistol_Stim`

## DataLibrary

- `ProjectileWeaponComponentData` DL hash used by FileDiver-era parser: `0x45171B68`
- Current FileDiver-era ProjectileWeapon record size observed in source/pattern definitions: `0x180`
- Current FileDiver-era map geometry used during probing:
  - `436` map entries,
  - `16` bytes per map entry,
  - `219` records,
  - `0x180` bytes per record.

These geometry values were useful for falsification but were **not structurally resolved in the September live process**.
