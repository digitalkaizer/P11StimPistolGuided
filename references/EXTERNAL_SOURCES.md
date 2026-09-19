# External Sources

## Bingus Shared Loader
- https://github.com/CowboyBingus/BingusSharedLoader

Relevant facts used during this project:
- v15+/API1 addon discovery.
- Addon source header format:
  `-- HD2-Addon: mods/<author>/<name>`
- Shared loader owns its boot/Wwise path; standalone addons should use unique resource paths rather than replacing its boot resource.

## FileDiver
- https://github.com/xypwn/filediver
- Projectile weapon parser:
  https://github.com/xypwn/filediver/blob/master/datalibrary/projectile_weapon_component.go
- Weapon data parser:
  https://github.com/xypwn/filediver/blob/master/datalibrary/weapon_data_component.go
- Seeking missile parser:
  https://github.com/xypwn/filediver/blob/master/datalibrary/seeking_missile_component.go

Caution:
The public FileDiver history inspected during this project included an August 12, 2026 update for game update `01.007.000`. Do not assume its embedded generated data exactly matches a later September game build without verification.

## HelldiversData / data.helldivers.io
- https://github.com/shalzuth/HelldiversData
- https://data.helldivers.io/

Useful current JSON paths:
- https://raw.githubusercontent.com/shalzuth/HelldiversData/master/data/entities/ProjectileWeaponComponentData.json
- https://raw.githubusercontent.com/shalzuth/HelldiversData/master/data/entities/WeaponDataComponentData.json
- https://raw.githubusercontent.com/shalzuth/HelldiversData/master/data/entities/SeekingMissileComponentData.json
- https://raw.githubusercontent.com/shalzuth/HelldiversData/master/data/entities/SensorEyeComponentData.json
- https://raw.githubusercontent.com/shalzuth/HelldiversData/master/data/entities/EntityComponentMap.json
- https://raw.githubusercontent.com/shalzuth/HelldiversData/master/data/entities/GuidanceTargetComponentData.json

This source was useful because it exposes already-decoded component JSON rather than requiring broad in-process scans.
