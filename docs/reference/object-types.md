---
title: Object types
description: Complete reference for every Object Type the EDM panel can assign to an object.
---

# Object types

Every object in the EDM exporter has an **Object Type** set via the EDM panel context menu. The type determines how the engine treats the object — geometry, helper, light, collision, decal, etc.

Default for all new objects is `Unknown`, which covers visible geometry and unspecified helper objects.

---

## Quick reference

| Object Type | Visible in-game? | Used for | Layer / location |
|---|---|---|---|
| `Unknown` | Yes (if mesh) | Default — visible geometry and generic empties | LOD layers |
| `Connector` | No | Named attachment points (weapons, mounts, effects) | LOD 0 only |
| `Bounding Box` | No | Box covering model + all animation extents | Root export folder |
| `User Box` | No | Box covering only the static model | Root export folder |
| `Collision Shell` | No (used for physics) | Surface collision wrapping the model | Collision layer |
| `Collision Line` | No (used for physics) | Vertex line for fuselage / landing gear outlines | Collision layer |
| `fake_light` | Yes (renders as glow) | Image-based light point (omni or spot) | LOD 0 only |
| `number_type` | Yes | UV-shifted digit decal for hull numbers / tail codes | Any LOD |

---

## Detailed entries

### Unknown

The default. Used for:

- All visible geometry (hull, wings, turrets, etc.)
- Helper empties used purely for organisation or transform parenting

The exporter treats meshes as renderable and empties as transform helpers based on their Blender type, not the EDM Object Type.

### Connector

Named attachment points. The engine, scripts, and other tools reference connectors by name to attach weapons, mount points, particle effects, or programming hooks.

- **Empty type:** Arrows recommended (visible orientation).
- **Naming:** specific and descriptive (the engine references this name).
- **LOD:** LOD 0 only. Not duplicated to lower LODs.
- **Connector Extensions field:** carries additional properties for engine-side or scripting consumption.

> Unlike the older 3ds Max exporter, no manual Z/Y axis rotation is needed. Blender's exporter handles axis convention conversion automatically.

See [Geometry & Animation — Connectors](../02-geometry-animation.md#connectors).

### Bounding Box

Empty cube enclosing the **whole model and its full range of animation**. Used by the engine for high-level spatial queries (visibility culling, weapon range checks).

- Add an empty cube.
- Resize to enclose all animated extents (e.g. when the gun elevates fully and the turret rotates fully).
- Place in the **root export folder**, not nested inside an LOD.
- Required for [bone export](../02-geometry-animation.md#bone-export).

### User Box

Empty cube enclosing **only the static model** (without animation extents). Used for purposes like UI selection bounds or hit detection that should match the visible silhouette rather than the animated volume.

- Add an empty cube.
- Resize to enclose the static model.
- Place in the root export folder.

### Collision Shell

Simplified mesh used for physics and damage calculations. Wraps the visible geometry but is much lower-poly.

- Lives in a dedicated **Collision** layer alongside the LOD layers.
- Has its own animation empties so collision tracks with movable parts.
- Use for the bulk of the model's collision volume.

### Collision Line

Vertex line (a mesh of edges, not faces) defining outlines used for specific physics queries.

- Typical use: fuselage centrelines, landing gear contact lines.
- Same layer organisation as collision shells.

### fake_light

Image-based light point. The vertices of a fake_light mesh become individual lights in-game.

- Object Type: `fake_light`.
- Material group: `EDM_fake_omni_material` (non-directional) or `EDM_fake_spot_material` (directional).
- For spot fakes, requires a child empty named `light_dir`.
- LOD 0 only.

See [Lighting & Damage — Fakes](../04-lighting-damage.md#fakes-light-points).

### number_type

A UV-shifted decal for rendering numerals (hull numbers, tail codes, vehicle IDs).

- Duplicate the part of the hull where the number appears.
- Duplicate the material (the number needs a base layer to overlay on).
- Create a decal-channel UV map placed over the first symbol in the texture atlas.
- Set Object Type to `number_type`.
- Assign the argument that shifts the UV.
- Set the shift value to the position of the last symbol in the atlas.

See [Lighting & Damage — Numbers](../04-lighting-damage.md#numbers-hull-numbers-and-tail-codes).

---

## Selecting an object type

In the EDM context menu, the Object Type dropdown shows all available values. Changing the type also changes the available settings on the object — for example, `fake_light` exposes texture coordinate fields, `number_type` exposes axis and shift fields.

If you change an object's type and lose access to settings you previously configured, those settings are still saved on the object — they reappear when the type is changed back.

---

## Cross-references

- [Naming conventions](naming-conventions.md)
- [Geometry & Animation](../02-geometry-animation.md)
- [Lighting & Damage](../04-lighting-damage.md)
