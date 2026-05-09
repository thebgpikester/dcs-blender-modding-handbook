---
title: EDM material inputs
description: Consolidated reference for material inputs across the default EDM material, the Glass material, and the Deck material.
---

# EDM material inputs reference

Consolidated reference for material inputs across the default EDM material, the Glass material, and the Deck material.

> **Preview-only** controls affect Blender's viewport display but are not written to the exported `.edm` file.

---

## Default EDM material

| Input | Type | Affects export? | Description |
|---|---|---|---|
| **Base Color** | Texture / colour | Yes | Albedo. If no texture, the colour value sets a flat object colour |
| **Base Alpha** | Texture | Preview only | For viewport transparency display |
| **Decal Color** | Texture | Yes | Second colour layer (dirt, leaks, surface details) on the decal UV |
| **Decal Alpha** | Texture | Preview only | For viewport decal transparency |
| **Roughness Metallic (RMC)** | Texture | Yes | Packed roughness/metallic/AO. Each channel carries a different map |
| **AO Value** | Slider | Preview only | Visually overlays AO on colour for viewport preview |
| **Light Map** | Texture | Yes | Baked lighting/AO on a separate UV map |
| **Light Map Value** | Slider | Preview only | Strength of light map in viewport |
| **Normal Map** | Texture | Yes | Standard tangent-space normal |
| **Normal Encoding** | Toggle (DirectX / OpenGL) | Preview only | Switches viewport normal interpretation |
| **Emissive** | Texture | Yes | Emission texture |
| **Emissive Mask** | Texture / value | Yes | Defines where emission is active |
| **Emissive Value** | Slider | Yes | Emission intensity |
| **FLIR** | Texture | Yes | Infrared appearance of the object |
| **DM_base** | Texture | Yes | Damage albedo |
| **DM_normal** | Texture | Yes | Damage normal |
| **DM_map** | Texture | Yes | Damage animation mask (anything not black is visible; >0.85 is transparent) |
| **Damage Visibility** | Slider | Preview only | Preview damage state in viewport |
| **Decal ID** | Number | Yes | Draw order — higher number renders later |
| **Opacity Value** | Slider | Yes | Transparency multiplier. Animatable for fade in/out |
| **Material Version** | Number | (Internal) | Update via **Update EDM Materials** if export errors mention version |

### Outputs

| Output | Purpose |
|---|---|
| **BSDF (main)** | Connect to Material Output → Surface. Required for correct viewport |
| **Per-channel previews** | Preview AO, roughness, metallic, etc. in isolation |

---

## Glass material (`EDM Glass`)

| Input | Affects export? | Description |
|---|---|---|
| **Glass Color / Color Filter** | Yes | Albedo of the glass itself — the colour of the glass, not what's on it |
| **Diffuse Color / Dirt** | Yes | Colour of dirt and leaks on the glass. Connect alpha as well |
| **Roughness Metallic** | Yes | Standard packed RM input |
| **Normal / Scratches** | Yes (in-game) | Mainly used for scratches. **Does not display in Blender's viewer** |
| **FLIR** | Yes | Same as default material |
| **Damage Textures** | Yes | DM_base, DM_normal, DM_map — same as default material |
| **Shadow Caster** | Yes | Shadow rendering mode |
| **Transparency** | Yes | Export transparency setting |
| **Glass Type** | Yes | `Cockpit` or `Instrumental` |

---

## Deck material

A two-layer composite material for ship and carrier decks.

### Layer 1 — tiled base

Repeating texture (planks, grating, metal plating).

| Input | Description |
|---|---|
| Base Color | Tiled albedo |
| Roughness Metallic | Tiled RMC |
| Normal | Tiled normal |

Uses the **first UV map**, typically tiled horizontally.

### Layer 2 — unique decal

Per-deck variation (dirt, streaks, tyre marks, weathering).

| Input | Description |
|---|---|
| Base Color | Unique albedo on second UV |
| Roughness Metallic | Unique RMC on second UV |
| Wet Map | Wetness mask on second UV |
| Damage Textures | DM_base, DM_normal, DM_map (optional) |

Uses the **second UV map**. Each second-layer texture node must have a UV Map node connected to it pointing to the second UV.

---

## Texture format quick reference

| Format | Use for |
|---|---|
| **BC1 DDS** | Albedo without alpha, light maps |
| **BC3 DDS** | Albedo with alpha, packed RMC |
| **BC5 DDS** | Normal maps |

> **Non-Color mode** must be set in Blender on roughness, metallic, normal, light map, and damage textures for correct viewport display.

See [Materials & Textures — Texture format requirements](../03-materials-textures.md#texture-format-requirements) for full discussion.

---

## Cross-references

- [Materials & Textures](../03-materials-textures.md)
- [Lighting & Damage — Texture damage](../04-lighting-damage.md#texture-damage)
- [Naming conventions](naming-conventions.md)
