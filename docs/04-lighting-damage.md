---
title: Lighting, damage, and additions
description: Real lamps, fake light points, surface mode, rabbit lights, the texture and geometry damage systems, and number decals for hull numbers and tail codes.
---

# Lighting, damage, and additions

This chapter covers the in-game lighting system (real lamps, fake light points, surface mode, rabbit lights), the model damage system (texture-based and geometry-based), and the number decal system used for hull numbers and tail codes.

> **Source attribution:** Derived from Eagle Dynamics' *Blender Exporter Part 4* video and supplemented with community knowledge.

---

## Lighting

The exporter supports three distinct lighting types:

- **Lamps** — actual light sources that illuminate other geometry.
- **Fakes (light points)** — image-based glowing points or plates.
- **Emission** — material-based glow (covered in [Materials & Textures](03-materials-textures.md)).

### Lamps

Lamp objects, like connectors, are exported only for **LOD 0**. The exporter transfers lamp characteristics as accurately as possible, but rendering differences mean final tuning must be done in Model Viewer.

#### Prerequisites

- **Render engine:** EEVEE. Cycles lacks some required settings.
- **Viewport:** Material Preview mode with Scene Lights enabled, or Rendered mode.
- **HDRI:** in Material Preview mode, the HDRI environment map is enabled automatically and can be dimmed or disabled. In Rendered mode, set the HDRI manually.

> See [Blender's lighting documentation](https://docs.blender.org/manual/en/latest/render/lights/index.html) for general lamp setup.

#### Supported lamp types

- **Point** (omni)
- **Spot**

#### Exported properties

| Property | Applies to | Description |
|---|---|---|
| Color | Point, Spot | Lamp colour |
| Power | Point, Spot | Lamp intensity |
| Specular | Point, Spot | Ability to cast highlights |
| Custom Distance | Point, Spot | Working range. If unchecked, default is 20 m |
| Size | Spot only | Angle of the light cone |
| Blend | Spot only | Softness of the light edge. 0 = hard, 1 = soft. Defines the inner angle |

#### Animating lamp properties

Animation arguments for lamp characteristics are set in the EDM Export panel. Create the animation first, then assign its argument in the EDM panel.

### Fakes (light points)

Fakes are image-based light points. Two creation modes:

- **Standard:** vertices of geometry become light points.
- **Surface mode:** quad plates become light points; the face normal defines the direction. Enabled via the Surface Mode checkbox.

#### Standard fakes — setup

1. Create geometry whose vertices will become the fakes.
2. Set the Object Type to `fake_light`.
3. Define the texture atlas region using `text_coord_LB` (left-bottom) and `text_coord_RT` (right-top). This sets the UV layout.
4. **Size** sets the size of the light plate.
5. **Object Luminance** sets the brightness of the glow.
6. To animate, specify the argument name in the action's name (see [Geometry & Animation — Animation arguments](02-geometry-animation.md#animation-arguments)).

#### Fake type — material group

Define whether the fake is directional or non-directional by assigning a material group:

- `EDM_fake_omni_material` — non-directional point.
- `EDM_fake_spot_material` — directional spot.

Connect the texture to the emissive channel of the selected group.

#### Material properties

| Property | Purpose |
|---|---|
| **Luminance** | Brightness or glow strength. Set on the material to apply to all objects using it, or on the object to affect only that instance |
| **Min Size Pixels** | Minimum on-screen fake size in pixels when far from the camera |
| **Max Distance** | Distance after which the light disappears |
| **Shift to Camera** | Shifts the light plate toward the camera to avoid visible intersections with geometry |
| **Outer Angle** (spot) | Outer limit of directional light, beyond which the light does not appear |
| **Inner Angle** (spot) | Angle of maximum brightness |

#### Spot direction empty

For `fake_spot`, direction is defined by an Empty parented to the fake's geometry:

- Use the **Arrows** empty type — the direction is easier to see.
- Name the empty exactly `light_dir`. The empty's X-vector defines the lamp direction.

#### Surface mode (alternative spot method)

Surface mode creates fake spots from square planes. The plane's centre is the fake centre; the face normal is the direction. Only quads are used.

1. Create the plane geometry. Plates may be separate or merged into one object.
2. Enable the **Surface Mode** checkbox.
3. Create the material as for a regular spot fake.
4. Set texture coordinates by unwrapping the UV map. **Size is determined by the plate's actual dimensions.**
5. Adjust plate size using the **Individual Origins** pivot point for convenience.
6. To set a backside texture: create a new UV map named `back` and adjust its UV coordinates.

Surface mode works well for complex setups with many lamps facing different directions.

### Rabbit lights (sequenced lights)

Rabbit lights are running lights along a sequence of lamps — used for runway lights and aircraft carrier deck lights. Works with both omni and spot fakes.

#### Setup

1. Create a brightness animation common to all lights by animating **Object Luminance**. Example: three keyframes — 1 → 5 → 1 — creating a flash.
2. Set the argument number in the action name.
3. Create a vertex group named `delay` and assign all vertices weight 0.
4. Adjust each vertex's weight individually. Range **0–1**, where 1 is the full duration of the animation.
5. Vertices with delay 0 fire at the start; vertices with 0.5 fire halfway through.

> **Tip:** adjust and verify vertex weights via the **Item** menu in the N-panel.

> See [Blender's vertex groups documentation](https://docs.blender.org/manual/en/latest/modeling/meshes/properties/vertex_groups/index.html).

---

## Model damage system

Two damage types are supported, and they may be combined:

- **Texture damage:** damage gradient applied through textures.
- **Geometry damage:** entire geometry replaced with a damaged version via visibility animation.

### Texture damage

Texture damage uses three special textures connected to the object's material:

| Texture | Purpose |
|---|---|
| `DM_base` | Albedo of the damage pattern |
| `DM_normal` | Normal map of the damage |
| `DM_map` | Mask containing animation frames of the damage. Anything not black is visible. Anything brighter than 0.85 renders as a transparent hole |

#### Important: DDS format change

**Blender does not support 3D volumetric DDS textures**, which were previously used to store four damage frames. The current format is **BC3 DDS**: frames 1–3 are stored as RGB layers, frame 4 in the alpha channel.

#### Material setup

- Connect each damage texture to the appropriate material input.
- For correct preview, connect the alpha channel of `DM_map` — this contains the fourth damage layer. Preview-only; does not affect export.
- Use the **Damage Visibility** slider to check how damage appears in the viewport.

#### Assigning the damage argument

Two **mutually exclusive** methods:

- **Whole-object damage:** set the damage argument in the EDM panel. The entire object breaks together.
- **Partial damage (vertex groups):** create vertex groups named `DMG_<n>` where `<n>` is the damage argument index. Assign the relevant geometry to each group. Multiple groups may be created — one per damage part or argument.

> **Caution:** these methods cannot be combined on the same object. If using vertex groups, the EDM panel damage argument must not be set.

The vertex-group method is convenient because the object does not need to be split into separate parts. Verify the result in Model Viewer.

### Geometry damage

Geometry damage replaces the whole geometry with a damaged version using visibility animation. Often used together with texture damage and shares the same argument — the model damages gradually via textures, then breaks completely when the geometry swap fires.

#### The seam problem

Splitting geometry to destroy a section produces visible seams and broken shading. Two solutions:

#### Method 1: Custom Split Normals

1. In the **Object Data Properties** (Geometry Data section), enable **Custom Split Normal** via the **Add Custom Split Normals Data** button. This freezes the current shading.
2. After applying, separate the geometry: `Y` (Edge Split) then `P` (Separate).

> **Trade-off:** to fix seams later, you must merge everything back, run **Clear Custom Split Normals Data**, and redo the process.

> See [Blender's normals documentation](https://docs.blender.org/manual/en/latest/modeling/meshes/editing/mesh/normals.html).

#### Method 2: Data Transfer modifier

This modifier transfers data — including geometry normals — from a clean reference object.

1. Create a copy of the separated elements stitched into a single seamless object. Move it to a separate working layer.
2. On each split object, add the **Data Transfer** modifier.
3. Set the clean stitched object as the source.
4. Enable **Face Corner Data** and turn on **Custom Normals**.
5. To copy the modifier across all split objects: select all objects with the configured object as the active (last selected), then press `Ctrl + L` → **Copy Modifiers**.
6. Verify in Model Viewer — seams should be gone.

> See [Blender's Data Transfer modifier documentation](https://docs.blender.org/manual/en/latest/modeling/modifiers/modify/data_transfer.html).

---

## Numbers (hull numbers and tail codes)

**Number Type** is a tool for rendering numerals on vehicles by offsetting the UV map. It is similar to the UV map animation method ([Materials & Textures — UV animation](03-materials-textures.md#uv-animation)) but optimised for digit rendering. UV shifts only in the decal channel.

### Setup

1. Duplicate the part of the hull where the number will appear. Adjust the geometry as needed.
2. Create a UV map for the decal channel. Place the UV island over the first symbol in the texture atlas (e.g. `0`).
3. **Duplicate the material.** This is required because the number needs a base layer to overlay on, and the duplicated material will also retain damage if present.
4. Add the decal containing the numbers and assign the new UV map to it.
5. In the EDM panel, set the Object Type to `number_type`.
6. Assign the argument that will shift the UV along the X or Y axis.
7. Set the shift value to the position of the last symbol in the atlas.
8. Optionally add a damage argument.
9. Export and verify.

### Worked example: vertical 0–9 + empty atlas

For a vertical atlas of digits 0–9 plus one empty slot (11 cells total):

```
Shift value = 10 / 11 = 0.909
```

The argument shifts the UV from position 0 (digit `0`) to position 0.909 (the empty slot at the bottom).

---

## Resources

- [Blender lighting](https://docs.blender.org/manual/en/latest/render/lights/index.html)
- [Blender vertex groups](https://docs.blender.org/manual/en/latest/modeling/meshes/properties/vertex_groups/index.html)
- [Blender normals](https://docs.blender.org/manual/en/latest/modeling/meshes/editing/mesh/normals.html)
- [Blender Data Transfer modifier](https://docs.blender.org/manual/en/latest/modeling/modifiers/modify/data_transfer.html)
- [Naming conventions reference](reference/naming-conventions.md)
- [Object types reference](reference/object-types.md)

---

> *Previous: [Materials and textures](03-materials-textures.md) — Next: [FBX pipeline (planned)](05-fbx-pipeline.md)*
