# Naming conventions

Names that the EDM exporter recognises by exact string matching, plus the conventions for material and object names that the exporter expects but does not enforce.

> **General rule:** all names use **Latin characters only** with **underscores** in place of spaces. This applies to file names, materials, textures, objects, layers, and Actions.

---

## Exact-match names (recognised by the exporter)

These names must be spelled exactly as shown. The exporter's behaviour depends on string matching against these.

### Empties and helper objects

| Name | Type | Purpose |
|---|---|---|
| `light_dir` | Empty (Arrows recommended) | Direction empty for `fake_spot` lights. The X-vector points the lamp direction |

### Vertex groups

| Name | Purpose |
|---|---|
| `delay` | Per-vertex timing offsets for [rabbit lights](../04-lighting-damage.md#rabbit-lights-sequenced-lights). Weights 0–1 across full animation duration |
| `DMG_<n>` | [Partial damage](../04-lighting-damage.md#assigning-the-damage-argument), where `<n>` is the damage argument index. Multiple groups (one per part) supported |

### UV map names

| Name | Purpose |
|---|---|
| `back` | Backside UV for surface-mode fake lights, when both faces of a plate need different textures |

### Texture inputs (material-side)

The damage system expects these texture roles:

| Name role | Purpose |
|---|---|
| `DM_base` | Damage albedo |
| `DM_normal` | Damage normal map |
| `DM_map` | Damage animation mask. Anything not black is visible; brighter than 0.85 renders transparent |

### Object Type values

These are EDM panel dropdown values, set per-object:

| Value | Used for |
|---|---|
| `Unknown` | Default — geometry and helper empties |
| `Connector` | Named attachment points (LOD0 only) |
| `Bounding Box` | Box covering model + all animations |
| `User Box` | Box covering only the static model |
| `Collision Shell` | Surface collision wrapping geometry |
| `Collision Line` | Vertex line for fuselage / landing gear outlines |
| `fake_light` | Image-based light point |
| `number_type` | Number decal with UV-shift digit selection |

For full descriptions see [Object types reference](object-types.md).

### Material groups

Created via `Shift + A → EDM Materials` in the Shader Editor:

| Group name | Purpose |
|---|---|
| `EDM_fake_omni_material` | Non-directional fake light material |
| `EDM_fake_spot_material` | Directional fake light material |
| `EDM Glass` | Glass material (cockpit canopies, instrument glass) |
| (Default EDM material) | Standard PBR material with full input set |

---

## Convention names (exporter-recognised by parsing)

These names follow a structured format the exporter parses.

### Action names (animation arguments)

```
<argument_number>_<description>
```

| Example | Argument | Description |
|---|---|---|
| `0_Wheel_Rotation` | 0 | Wheel rotation |
| `1_Gun_Elevation` | 1 | Gun elevation |
| `200_Damage` | 200 | Damage |

The number is mandatory; the description is for clarity only and is ignored by the engine.

> **Without a numbered prefix, the animation will not export.** No warning is given — the animation is silently dropped.

### LOD layer names

```
LOD<index>_<distance_in_metres>
```

| Example | Index | Render distance |
|---|---|---|
| `LOD0_50` | 0 | Up to 50 m |
| `LOD1_150` | 1 | Up to 150 m |
| `LOD2_500` | 2 | Up to 500 m |
| `LOD3_5000` | 3 | Up to 5000 m |

LOD layers must be **independent** — no parent-child relationships between LOD trees.

---

## Convention names (exporter-expected but not enforced)

These do not break the export if missed but are required for organisation, integration with other tools, and engine-side reference.

### Materials

Material names should include the model name as prefix or suffix:

```
<model_name>_<part>
```

| Example | Model | Part |
|---|---|---|
| `KS19_body` | KS-19 anti-aircraft gun | Body |
| `Mig21_wing` | MiG-21 | Wing |
| `I58_hull` | I-58 submarine | Hull |

This makes materials searchable across complex scenes and prevents accidental name collisions when multiple models load.

### Object names

Key objects should have clear, descriptive names. Examples from the ED tutorials:

- `gun`, `barrel`, `body`, `hull`, `turret`, `wheel_FL`, `wheel_FR`

Avoid Blender's auto-numbered defaults (`Cube.001`, `Cube.002`). They are not informative and they break across re-imports.

### Texture file names

Texture file names should reflect their role and the model:

```
<model>_<part>_<channel>.dds
```

| Example | Channel meaning |
|---|---|
| `I58_hull_co.dds` | Albedo (colour) |
| `I58_hull_nm.dds` | Normal map |
| `I58_hull_roughmet.dds` | Packed roughness/metallic |

> **Community convention:** the suffixes `_co`, `_nm`, and `_roughmet` are widely used in DCS modding for albedo, normal, and packed RM textures. They are not enforced by the exporter but are expected by many community tools and skin authors.

---

## Forbidden patterns

- **Non-Latin characters** in any name. Causes silent export failures or runtime crashes.
- **Spaces** in file or asset names. Use underscores.
- **Mixed case in role identifiers** when the exporter does string matching (`light_dir` not `Light_Dir`).
- **Reusing exact names across LODs** in conflicting hierarchies. The exporter will resolve names by layer membership, but ambiguity makes debugging painful.

---

## Cross-references

- [Geometry & Animation — Animation arguments](../02-geometry-animation.md#animation-arguments)
- [Lighting & Damage — Texture damage](../04-lighting-damage.md#texture-damage)
- [Lighting & Damage — Rabbit lights](../04-lighting-damage.md#rabbit-lights-sequenced-lights)
- [Object types reference](object-types.md)
