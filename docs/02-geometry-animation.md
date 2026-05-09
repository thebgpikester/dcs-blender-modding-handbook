---
title: Geometry and animation
description: Preparing geometry, building empty-driven animation hierarchies, organising LODs, animation arguments, bounding boxes, collision, connectors, and bone export.
---

# Geometry and animation

This chapter covers preparing geometry for export, building empty-driven animation hierarchies, organising LODs, animating with arguments, and creating the supporting scene elements (bounding boxes, collision geometry, connectors, and bones).

> **Source attribution:** Derived from Eagle Dynamics' *Blender Exporter Part 2* video and supplemented with community knowledge.

<iframe width="800" height="450" src="https://www.youtube.com/embed/MYGZH5sG_0M" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

---

## Geometry preparation

A model is "ready for export" when:

- It is split into separate parts that match its animation needs (each rotating part is its own object).
- Pivots are correctly positioned at each part's actual centre of rotation.
- The whole model faces along the positive X axis.
- All transforms are applied.

### Setting pivots

The simplest way to set a pivot is via the 3D cursor:

1. Select a vertex, edge, or face that defines the rotation centre (e.g. the centre vertex of a wheel).
2. Press `Shift + S` and choose **Cursor to Selected**. The 3D cursor jumps to that location.
3. In Object Mode, right-click the object and choose **Set Origin > Origin to 3D Cursor**.

The object's pivot is now at the chosen point.

> See [Blender's documentation on the 3D cursor](https://docs.blender.org/manual/en/latest/editors/3dview/3d_cursor.html) and [origin settings](https://docs.blender.org/manual/en/latest/scene_layout/object/origin.html).

### Verifying axis orientation

Models must face along positive X. To verify:

1. Open the navigate function (the gizmo in the top-right of the 3D viewport).
2. Enable display of the X and Y axes.
3. Confirm the model's "front" lines up with `+X`.

### Applying transforms

Before exporting, every object's Location, Rotation, and Scale must be applied:

- Select all objects.
- Press `Ctrl + A` and apply Rotation and Scale (Location is also typically applied for static objects).

> **Common mistake:** rotating an object in Object Mode without applying the rotation. The object looks correct in the viewport but exports with a hidden rotation offset, producing in-game animations that rotate around the wrong axis.

---

## Hierarchies and empties

DCS animations are driven by **Empties** (helper objects that act as a skeleton). Geometry is parented to empties, and animations are applied to the empties — not directly to geometry.

### Why empties

- A single empty drives a single argument cleanly. Animating the geometry directly conflates rotation with mesh data.
- Empties can be reorganised, replaced, or recycled across models without disturbing the geometry.
- Complex hierarchies (turret rotates → gun elevates → barrel recoils) become readable.

> See [Blender's documentation on empties](https://docs.blender.org/manual/en/latest/modeling/empties.html).

### Creating and parenting

1. Add an empty (`Shift + A > Empty`). The **Arrows** type is recommended because the orientation is visible.
2. Position it where the rotation should originate.
3. Select the **child object first**, then **Shift-select the parent empty**.
4. Press `Ctrl + P` and choose **Object**.

> The parenting hotkey **only works with the mouse cursor over the 3D viewport**. If you press `Ctrl + P` while hovering over the Outliner or another panel, nothing happens.

### What goes on the empty vs the geometry

| Animation type | Apply to empty | Apply to geometry |
|---|---|---|
| Movement (translation) | Yes | — |
| Rotation | Yes | — |
| Scale | Yes | — |
| Visibility | — | Yes |
| Material property animation | — | Yes |

Transform animations (the first three) belong on empties — one empty per animation argument. Other animation types (visibility, material colour shifts) are applied directly to the geometry.

---

## LOD management

LODs (Levels of Detail) are organised using specific layer naming conventions in the Outliner. The DCS engine uses the LOD layer name to decide which mesh to render at a given camera distance.

### Naming convention

```
LOD<index>_<distance>
```

| Layer name | Meaning |
|---|---|
| `LOD0_50` | LOD 0 — rendered up to 50 metres |
| `LOD1_150` | LOD 1 — rendered up to 150 metres |
| `LOD2_500` | LOD 2 — rendered up to 500 metres |
| `LOD3_5000` | LOD 3 — rendered up to 5000 metres |

Distance specifiers can be tuned to the model's complexity and silhouette — the values above are the ED reference example, not a fixed rule.

### Critical rule: LOD layer independence

LOD layers must be independent of each other. **There must be no parent-child relationships crossing LOD boundaries.** If an object in `LOD0_50` is parented to an empty in `LOD1_150`, the exporter raises an error.

If your animation hierarchy is shared across LODs (turret empties drive both the LOD0 mesh and the LOD1 mesh), duplicate the empty into each LOD layer rather than parenting across.

### Two methods of exporting LODs

The exporter supports two LOD packaging strategies, and the choice matters for how the model integrates with DCS.

#### Whole method (single file)

All LODs are exported into a single `.edm` file, separated internally by layer membership and distance markers.

- **Pros:** one file to manage, simpler distribution.
- **Cons:** the file is monolithic — replacing a single LOD requires re-exporting everything.
- **Constraint:** LOD layers must not overlap hierarchically. Each LOD must be self-contained, with no parent-child links between LOD trees. Overlaps trigger an export error.

#### Separated method (multiple files)

Each LOD is exported as an individual `.edm` file. Collision geometry is also exported separately.

- A `.lods` text file is used to define how the separate `.edm` files relate.
- **Pros:** individual LODs can be re-exported and replaced independently.
- **Cons:** more files to manage; the `.lods` text file becomes a piece of pipeline metadata you must maintain.

> **Verification needed:** the exact format of the `.lods` text file is not detailed in the source video. If you have a working example, please contribute it via [content addition issue](https://github.com/thebgpikester/dcs-blender-modding-handbook/issues/new?template=content_addition.md).

---

## Animation workflow

### Timeline setup

Before starting any animation work:

1. Open the timeline window.
2. Click **Reset** in the EDM Export panel. This sets:
   - Start frame: 0
   - End frame: 200
   - Playhead: frame 100 (the **neutral / zero position** of every argument)
3. Confirm Blender's default keyframe interpolation is set to **Linear** (see [Installation — Animation interpolation](01-installation.md#3-animation-interpolation)).

Frame 100 as the neutral position is a DCS convention. An argument at value 0 corresponds to frame 100. Negative-direction motion runs from frame 100 toward frame 0; positive runs from frame 100 toward frame 200.

### Animation arguments

Every animation must be assigned an **argument number** to be recognised by the DCS engine. The number is encoded into the **Action name** in the **Action Editor**.

#### Naming convention

```
<argument_number>_<description>
```

| Action name | Argument | Description |
|---|---|---|
| `0_Wheel_Rotation` | 0 | Wheel rotation |
| `1_Gun_Elevation` | 1 | Gun elevation |
| `200_Damage` | 200 | Damage |

The description after the underscore is **optional and for clarity only** — the engine ignores it. The number prefix is mandatory.

> **Critical:** without a numbered prefix, the animation will not export at all. There is no warning — the animation is silently dropped.

#### When the first keyframe is created

Blender automatically generates an Action when the first keyframe is set on an empty. That Action becomes the carrier for the animation argument.

### The "first and last frame identical" trap

If the first and last keyframes of an animation hold identical transform values, the animation works correctly in Blender's viewport but **does not play in DCS's engine**.

The fix: insert at least a few intermediate keyframes with distinct values. For a 360° wheel rotation, do not animate `frame 0: 0°` → `frame 200: 360°` only. Add `frame 100: 180°` (or several intermediate frames) so the engine has distinct values to interpolate.

> This is one of the most common silent-failure causes in DCS animation work.

### Worked example: animating a 360° wheel rotation

1. Select the wheel's empty.
2. Click **Reset** in the EDM Export panel.
3. At frame 0, set rotation Y = 0°. Insert keyframe (`I`).
4. At frame 100, set rotation Y = 180°. Insert keyframe.
5. At frame 200, set rotation Y = 360°. Insert keyframe.
6. In the Action Editor, rename the auto-created Action to `0_Wheel_Rotation`.
7. In the EDM panel, type `0` and click **Set Argument** to verify the animation isolates correctly.

### Verifying animations

In the EDM Export panel:

- **Set Argument** with a number → activates only that argument; everything else is muted.
- **Mute All** → disables every animation.
- **Unmute All** → re-enables every animation.
- Individual checkboxes in the Animation window also toggle per-animation.

### Animation editor windows

Three editors are useful when working with animations:

| Editor | Best for |
|---|---|
| **Timeline** | Creating keyframes, scrubbing, assigning argument numbers via Action names |
| **Dope Sheet** in **Action Editor** mode | Detaching, replacing, or duplicating Actions |
| **Graph Editor** | Visualising and fine-tuning animation curves |

The Graph Editor is particularly useful for verifying linear interpolation and smoothing out unwanted curves between keyframes.

> See [Blender's animation editors documentation](https://docs.blender.org/manual/en/latest/editors/index.html#animation-editors) for full coverage.

### Visibility animation

Visibility is a per-object property and animates differently from transforms.

#### Setup

1. Hover the mouse cursor over the **Visibility** checkbox in the EDM Export menu.
2. Press `I` to insert a keyframe.

#### Behaviour rules

- **Visibility works between two "on" keyframes.** A single keyframe is not enough — you need a pair to define an interval.
- **Visibility is inherited.** If the parent has visibility animation that hides it, all child objects disappear with it.

### When Blender does not auto-create an Action

Some animated properties (typically those exposed by add-ons rather than core Blender) do not create a Blender Action when keyframed. In these cases:

- You cannot assign the argument number through the Action name (because there is no Action).
- Instead, create the animation as normal, then assign the argument number directly in the **EDM Export menu** for that object.

This is the route used for damage arguments and several material-property animations covered in [Lighting & Damage](04-lighting-damage.md).

---

## Bounding boxes and user boxes

Both are empty cubes used to define spatial extents for the model.

### Bounding box

Encompasses the entire model **including all animations** — when the wheels rotate or the gun elevates, the bounding box still contains the full motion volume.

- Add an empty cube in the export layer.
- Resize to enclose all animated extents.
- Set Object Type to **Bounding Box**.
- Give it a clear, model-specific name.

### User box

Covers only the model itself, not its animations.

- Add an empty cube.
- Resize to enclose the static model.
- Set Object Type to **User Box**.
- Name appropriately.

> Both bounding and user boxes must live in the **root export folder**, not nested inside an LOD layer.

---

## Collision geometry

Collision is a simplified mesh used for physics and damage calculations. It is separate from the visible LOD geometry.

### Layer placement

Inside the Export layer, create a dedicated **Collision** layer. The collision geometry has its own animation empties (e.g. so a gun's collision tracks with its elevation).

### Two collision types

| Type | Purpose | Object Type setting |
|---|---|---|
| **Collision Shell** | Surface collision wrapping the visible model geometry. Used for the bulk of physics queries. | `Collision Shell` |
| **Collision Line** | Vertex lines defining outlines — typically fuselage centrelines and landing gear contact lines. | `Collision Line` |

Each collision object must have a specific name and the correct Object Type assigned in the EDM context menu.

---

## Connectors

Connectors are empties that mark named points on the model — used for attaching weapons, mounting fuel tanks, anchoring effects, programming hookups, and similar.

### Setup

1. `Shift + A` and add an Empty. Any type works, but **Arrows** is recommended for visibility of orientation.
2. Position the empty correctly in the world.
3. Place it in the appropriate layer and verify the hierarchy.
4. Set Object Type to **Connector** in the EDM context menu.
5. Give it a clear, specific name (this is what the engine and other systems will reference).

### Connector Extensions field

The connector exposes a **Connector Extensions** field for programming-related properties. Use this when a connector needs to carry additional metadata for engine-side or scripting-side consumption.

### Critical rules

- **Connectors are LOD0 only.** They are not duplicated into lower LODs. The same applies to lamp objects (see [Lighting & Damage](04-lighting-damage.md#lamps)).
- This rule applies whether the model uses the [whole or separated LOD method](#two-methods-of-exporting-lods).

### Note for users coming from 3ds Max

Unlike the older 3ds Max exporter, you do **not** need to manually rotate the connector to align Z and Y axes. Blender's exporter handles the axis convention conversion automatically.

---

## Bone export

Bone-driven (skeletal) animation is supported. The setup is straightforward but has strict requirements.

### Setup

1. Select the geometry first.
2. Shift-select the armature.
3. Press `Ctrl + P` and choose **Armature Deform > With Automatic Weights** (or **With Empty Groups** if you want to set vertex weights manually).
4. Configure vertex weights as needed in Weight Paint mode.
5. Create the animation.
6. Assign an argument number via the Action name.

> See [Blender's Armatures documentation](https://docs.blender.org/manual/en/latest/animation/armatures/index.html) for the underlying tools.

### Critical rules

- **Maximum 4 bones can influence a single vertex.** Blender allows more, but the exporter enforces the 4-bone limit.
- **Object and armature transforms must both be reset** with `Ctrl + A`. Check both in the **Item** tab (`N` panel) — Location and Scale must be zero/identity.
- **Pivots of the object and armature must be aligned.** Mismatched pivots cause skeletal animations to drift in unexpected directions.
- **A bounding box is required** for bone export.

Unreset transforms or mismatched pivots are the two most common bone-export failure modes.

---

## Resources

- [Blender 3D cursor and origin documentation](https://docs.blender.org/manual/en/latest/editors/3dview/3d_cursor.html)
- [Blender empties documentation](https://docs.blender.org/manual/en/latest/modeling/empties.html)
- [Blender animation editors](https://docs.blender.org/manual/en/latest/editors/index.html#animation-editors)
- [Blender armatures documentation](https://docs.blender.org/manual/en/latest/animation/armatures/index.html)
- [Naming conventions reference](reference/naming-conventions.md)
- [Object types reference](reference/object-types.md)
- [Hotkey reference](reference/hotkey-reference.md)

---

> *Next: [Materials and textures](03-materials-textures.md)*
