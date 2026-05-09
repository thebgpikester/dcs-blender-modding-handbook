---
title: Materials and textures
description: DDS texture formats, the EDM shader node system, full material input reference, two-layer decals, light maps, UV animation, deck and glass materials.
---

# Materials and textures

This chapter covers texture format requirements, the EDM shader node system, the full material input/output reference, two-layer decals, light maps, UV animation, and the specialised Deck and Glass materials.

> **Source attribution:** Derived from Eagle Dynamics' *Blender Exporter Part 3* video and supplemented with community knowledge.

<iframe width="800" height="450" src="https://www.youtube.com/embed/Z76MHTwydlo" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

---

## Texture format requirements

To optimise game-engine performance, all textures must be converted to **DDS** format before export. The Photoshop DDS plugin is the reference tool, though any DDS-capable converter works.

| Format | Usage | Description |
|---|---|---|
| **BC1** | Albedo without alpha, light maps | Compressed format for textures with no alpha channel |
| **BC3** | Albedo with alpha, RMC (roughness/metallic packed) | Compressed format for textures that need an alpha channel — albedo with opacity, packed RM channels |
| **BC5** | Normal maps | Non-compressed format used for normal mapping |

### Non-Color mode rule

Set all non-colour textures to **Non-Color** mode in Blender for correct viewport display. This applies to:

- Roughness / Metallic / RMC textures
- Normal maps
- Light maps
- Damage textures (DM_normal, DM_map)
- Any other non-albedo, non-emissive texture

> The colour space setting only affects Blender's viewport. It does not influence export. The exporter reads from the texture file directly.

> See [Blender's documentation on color management](https://docs.blender.org/manual/en/latest/render/color_management.html).

---

## Material workflow

Material work happens in two places:

- **Materials tab** in the Properties editor — add slots, create or switch materials.
- **Shader Editor** — the node graph for textures, UVs, and animation.

> **Reminder:** ensure the Node Wrangler add-on is enabled. See [Installation — Node Wrangler](01-installation.md#required-add-on-node-wrangler).

### Basic material creation

1. Select the geometry.
2. Create a material in either the Materials tab or Shader Editor and give it a clear, model-specific name (see [Naming conventions](reference/naming-conventions.md)).
3. In the Shader Editor, delete the default Principled BSDF node.
4. Press `Shift + A` and open the **EDM Materials** submenu. Choose the required material group.
5. Connect the EDM node to the Material Output node's Surface channel. With Node Wrangler enabled, `Ctrl + Shift + LMB` on the EDM node performs this connection automatically.
6. Position the node, then drag-and-drop the required texture files into the editor.
7. Inputs and outputs are labelled — connect each texture to its matching input.

### Multi-slot materials

When an object uses several materials:

1. In the Materials tab, create multiple slots.
2. Assign a material to each slot.
3. In Edit Mode, select the geometry that should carry a given material and click **Assign**.

### Viewport preview

- Enable **Material Preview** mode to preview textures in the 3D viewport.
- For correct transparency preview, switch the render engine to **EEVEE** and set the material's **Blend Mode** (in the Material Settings tab) from `Opaque` to **Alpha Clip** or **Alpha Blend**.

> **Important:** the Blender Blend Mode is for *viewport preview only*. Export transparency is configured in the EDM material's **Transparency menu**, not via Blender's blend mode. The Transparency menu also exposes shadow caster settings.

---

## EDM material inputs reference

The default EDM material exposes a comprehensive set of inputs. Items marked as preview-only affect Blender's viewport but are not written to the exported model.

| Input | Purpose |
|---|---|
| **Base Color** | The base texture (albedo). If no texture is connected, the value sets the object's flat colour |
| **Base Alpha** | Transparency display in Blender's viewport only. Does not affect export |
| **Decal Color** | Second colour texture layer — adds dirt, leaks, or extra detail on top of the base |
| **Decal Alpha** | Decal transparency for viewport only. Does not affect export |
| **Roughness Metallic (AO/RMC)** | Packed roughness/metallic/AO texture. The AO Value slider visually overlays AO on the colour texture for preview only |
| **Light Map** | A separate texture baked with lighting or AO on a different UV map. The Light Map Value slider is viewport-only |
| **Normal Map** | Normal texture. The Normal Encoding slider switches between DirectX and OpenGL conventions — viewport only |
| **Emissive** | Emission texture. Includes Emissive Mask (defines glowing area) and Emissive Value (intensity) |
| **FLIR** | Texture showing how the object appears in infrared |
| **Damage Textures** | DM_base, DM_normal, and DM_map inputs. See [Lighting & Damage](04-lighting-damage.md#texture-damage) |
| **Decal ID** | Fixes draw order issues. Higher numbers render later |
| **Opacity Value** | Multiplier for transparency level. Animating it produces fade in/out effects |
| **Material Version** | Version number of the material. Update via the **Update EDM Materials** button in the EDM Export tab if export errors mention version mismatch |

For the consolidated reference also used by the Deck and Glass materials, see [EDM material inputs reference](reference/edm-material-inputs.md).

### Outputs

- **BSDF (main):** connects to the Material Output node's Surface channel. Required for correct viewport display.
- **Per-channel preview outputs:** allow individual channels (AO, roughness, metallic, etc.) to be previewed in isolation while debugging materials.

### Updating material versions

As the exporter evolves, EDM materials get new versions. If an export error mentions material version, click **Update EDM Materials** in the EDM Export tab. The version number of each material is shown at the bottom of the material in the Shader Editor.

---

## Decals (two-layer materials)

A decal is a second colour texture layer overlaid on the base material via an alpha channel — used for dirt, streaks, leaks, surface details.

### Setup

1. In the **Object Data Properties** (Data tab), create a second UV map for the object and give it a clear name.
2. Edit the UV in the UV editor. The texture's alpha channel can be previewed there.
3. In the Shader Editor, assign the decal texture to the Decal Color input.
4. Add a **UV Map** node and assign the second UV map to it. Wire the UV Map node into the decal texture node.

> **Default behaviour:** if a texture node has no UV Map node attached, the first UV channel is used by default.

### Important limitations

- **Color only:** the second layer can only carry a colour texture. Roughness/metallic and normal maps are **not yet supported** on the decal layer.
- **UV mismatch error:** if a material references a UV map that the object using it does not have, the exporter throws an error. Always verify the second UV map exists on every object using a decal material.

---

## Light maps

Light maps are baked lighting (or AO) stored on a separate UV map. They are mainly used for large objects or geometry with many duplicates.

### Setup

1. Create a new UV map for the object.
2. Bake lighting or ambient occlusion onto it. Multiple objects may share one large texture atlas.
3. Connect the baked texture to the **Light Map** input and assign the new UV via a UV Map node.
4. To overlay the light map on the object's colour in Blender's viewport, max out the AO Value and Light Map Value sliders.

### Resolution recommendation

Approximately **64 pixels per metre** of model surface area.

### In-game blending

In game, the light map blends with ambient occlusion from the roughness/metallic texture using a **darken** method — the darkest pixel between the two sources wins.

> See [Blender's baking documentation](https://docs.blender.org/manual/en/latest/render/cycles/baking.html) for the bake workflow itself.

---

## UV animation

UV movement and offset can be animated for effects like scrolling textures or rotating instrument needles.

### Setup

1. Select the texture node and press `Ctrl + T` (Node Wrangler) to add a Mapping node and a Texture Coordinate node, both pre-wired.
2. Add a **UV Map** node to define the coordinate source.
3. Press `I` with the mouse over the Mapping node's **Offset** or **Rotation** values to insert keyframes.
4. Open the N-panel (`N`), go to the **Node** tab, and enter the argument index number in the **Label** field. The Mapping node's name will change to that number.
5. Export and test.

> See [Blender's Mapping node documentation](https://docs.blender.org/manual/en/latest/render/shader_nodes/vector/mapping.html).

---

## Two-sided rendering

The **Two-Sided** function in the EDM Export panel forces the object to render from both sides — useful for thin geometry such as flags, sails, or single-plane decals.

---

## Deck material

A complex shader for ship and carrier decks, using two main layers.

### Layer composition

- **First layer (tiled):** base colour, roughness/metallic, and normal — typically a trim texture tiled horizontally for repeating planks or grating.
- **Second layer (unique):** a decal containing dirt, streaks, tyre marks, etc. Includes base colour, roughness/metallic, damage textures, and a **wet map**. Uses the second UV map.

### Build sequence

1. Connect the first layer's textures to base colour, roughness/metallic, and normal inputs.
2. Connect the second layer's textures to base colour, roughness/metallic, and wet map inputs.
3. Arrange the texture nodes in two columns — one per layer — for clarity.
4. Assign the second UV map to all second-layer textures using **UV Map** nodes.
5. If damage textures are used, set them up in the same pattern. See [Lighting & Damage — Texture damage](04-lighting-damage.md#texture-damage).
6. **Always verify** each second-layer texture has its UV Map node connected.
7. Use the Damage Visibility slider to preview damage in the viewport.

---

## Glass material

A PBR material optimised for glass surfaces — cockpit canopies, instrument glass, vehicle windows.

### Setup

1. Create the **EDM Glass** material group via `Shift + A` → EDM Materials.
2. Set Shadow Caster and Transparency parameters as in the default material.
3. Choose **Glass Type**:
   - `Cockpit` for cockpit glass
   - `Instrumental` for everything else (instrument faces, smaller glass elements)

### Glass material inputs

| Input | Purpose |
|---|---|
| **Glass Color / Color Filter** | The albedo of the glass itself — the colour of the glass, not dirt or leaks |
| **Diffuse Color / Dirt** | Colour of dirt, leaks, and surface contamination. Connect the alpha channel as well |
| **Roughness Metallic** | Standard packed RM input, used as in the default material |
| **Normal / Scratches** | Mainly used to render scratches. Scratches work in-game but **do not display in Blender's viewer** |
| **FLIR** | Same as default material |
| **Damage Textures** | Same as default material |

---

## Resources

- [Blender color management](https://docs.blender.org/manual/en/latest/render/color_management.html)
- [Blender Mapping node](https://docs.blender.org/manual/en/latest/render/shader_nodes/vector/mapping.html)
- [Blender baking](https://docs.blender.org/manual/en/latest/render/cycles/baking.html)
- [Blender Node Wrangler](https://docs.blender.org/manual/en/latest/addons/node/node_wrangler.html)
- [EDM material inputs reference](reference/edm-material-inputs.md)
- [Hotkey reference](reference/hotkey-reference.md)

---

> *Next: [Lighting and damage](04-lighting-damage.md)*
