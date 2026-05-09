---
title: Installation and configuration
description: Installing the EDM exporter add-on, configuring Blender, and a tour of the EDM Export panel.
---

# Installation and configuration

This chapter covers obtaining the EDM exporter add-on, installing it into Blender, configuring Blender so that exports work without per-project setup, and a tour of the EDM Export panel.

> **Source attribution:** Derived from Eagle Dynamics' *Blender Exporter Part 1* video and supplemented with community knowledge. Cross-referenced against the source transcript.

<iframe width="100%" height="600" src="https://www.youtube.com/embed/_Wf0bWwUn-A" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

---

## Prerequisites

### Blender version

Use a **Long-Term Support** release of Blender, specifically version **4.2 LTS** or **4.5 LTS**. Stable operation on non-LTS releases is not guaranteed.

> Blender's LTS policy: each LTS release receives critical bug fixes for two years. See [Blender LTS releases](https://www.blender.org/download/lts/) for the current support windows.

### Required add-on: Node Wrangler

Enable Blender's built-in **Node Wrangler** add-on. It ships with Blender by default but is not enabled out of the box.

To enable: **Edit > Preferences > Add-ons**, search for `Node Wrangler`, and tick the checkbox.

Node Wrangler provides shortcuts used throughout the materials chapter, particularly:

- `Ctrl + Shift + LMB` — connect any node directly to the Material Output
- `Ctrl + T` — add a Mapping node and Texture Coordinate node in one operation

The official ED tutorial recommends learning the main Node Wrangler hotkeys before starting on materials. See the [Node Wrangler documentation](https://docs.blender.org/manual/en/latest/addons/node/node_wrangler.html) for the full feature list.

---

## Installing the EDM exporter

1. Download the EDM exporter `.zip` file from the official Eagle Dynamics distribution channel (FTP or website). The version is shown in the file name.
2. In Blender, open **Edit > Preferences**.
3. Select the **Add-ons** tab.
4. Click **Install from Disk** and select the downloaded `.zip`.
5. In the search bar, type `EDM` and confirm the checkbox next to the add-on is ticked.

Once installed and enabled, a new **EDM Export** tab appears in the 3D Viewport's side panel (toggled with `N`).

---

## Initial configuration

These settings only need to be applied once if you save them as your Blender startup file.

### 1. Set the Model Viewer path

In the EDM add-on's preferences (within the Add-ons screen):

- Specify the path to the **DCS Model Viewer** executable in the **Executable Path** field.
- Optionally enable **Run viewer after model export** to auto-launch the viewer after every export.

Model Viewer is essential for validating models outside the simulator. Most export issues are easier to diagnose in Model Viewer than in DCS itself.

### 2. Study the Learning Demos

The EDM exporter ships with a folder of example `.blend` files demonstrating exporter features. Click **Open Learning Demo** in the EDM settings to open the folder.

Demos cover:

- Vehicle export
- Bone-driven animation
- Material setups
- Other key exporter functions

Eagle Dynamics explicitly recommends studying these alongside the tutorial videos. Each demo is a complete, working scene rather than a fragment — they are the closest thing to canonical reference material outside the videos themselves.

> **Tip:** Knowing the path to the Learning Demos folder also helps when [updating the add-on manually](#manual-add-on-update) — the `IO_Scene_EDM` folder is one directory up from the demos.

### 3. Animation interpolation

In **Edit > Preferences > Animation**, set the default keyframe interpolation to **Linear**.

DCS's animation system expects linear interpolation between keyframes. Blender's default Bezier interpolation produces subtly wrong in-game animation curves — a slow gun elevation will appear to "ease" into position rather than tracking proportionally with the input value.

### 4. Render engine

In 4.6 LTS the default render enging may already be EEVEE, but check that it is, in **Edit > Preferences > Render** (or per-scene under **Properties > Render**), set the engine to **EEVEE**.

EEVEE is required for several material previews to work correctly and is the only engine that supports the full set of EDM material features in viewport preview.

> The render engine affects what you see in Blender's viewport. It does not affect the exported `.edm` file's appearance in DCS, which uses its own renderer. EEVEE is purely a working-environment choice.

### 5. Save the startup file

To avoid repeating the above for every new project:

1. Adjust your viewport layout, panels, and any other working defaults.
2. **File > Defaults > Save Startup File**.

Every new Blender file will now open with the EDM exporter active and configured. See [Blender's documentation on the startup file](https://docs.blender.org/manual/en/latest/files/blend/startup_file.html) for what gets saved.

---

## The EDM Export panel

The main exporter controls live in the 3D Viewport's side panel under the **EDM Export** tab. Press `N` to toggle the side panel.

### Panel-level controls

| Control | Purpose |
|---|---|
| **Fast EDM Export** | Exports to the path of the current `.blend` file, or to a path specified in settings |
| **Manual Export** (**File > Export > EDM**) | Standard export dialog with full path selection |
| **Set Argument** | Displays a specific animation argument by number — disables all other animations to isolate one for preview |
| **Mute All** | Disables every animation on visible objects |
| **Unmute All** | Re-enables every animation on visible objects |
| **Reset** | Sets the timeline start to frame 0, end to frame 200, and the playhead to frame 100 (the neutral position) |
| **Update Materials** | Updates scene materials to the current exporter-compatible version |

#### Worked example: Set Argument

If your model has argument `0` driving turret rotation and argument `1` driving gun elevation, typing `1` and clicking **Set Argument** will isolate the elevation animation — every other argument is muted so you can preview elevation in isolation.

Animations can also be muted or unmuted individually via the checkbox in the Animation window.

#### When to use Update Materials

The exporter evolves and material versions advance over time. The version number of each material is shown at the bottom of the material in the Shader Editor.

If you attempt an export with an outdated material, the exporter raises an error. Click **Update Materials** to migrate every material in the scene to the current version, then re-export.

For the consolidated hotkey reference across all exporter operations, see [Hotkey reference](reference/hotkey-reference.md).

---

## The EDM context menu (per-object)

When an object is selected, the EDM panel shows a context menu of per-object parameters.

### Visibility

A checkbox controlling whether the object is visible in-game.

- Checkbox **on** = visible
- Checkbox **off** = hidden, along with all child objects

Visibility can be animated. See [Geometry & Animation — Visibility animation](02-geometry-animation.md#visibility-animation).

### Object Type

Sets the role of the object in DCS. Default is **Unknown**, which is used for geometry and helper objects (empties / dummies).

Other types include **Connector**, **User Bounding Box**, **Bounding Box**, **Collision Shell**, **Collision Line**, **Fake Light**, and **Number Type**. Each type changes the available settings on the object. See [Object types reference](reference/object-types.md) for the full list.

### Two-Sided Object

A checkbox that forces the object to render from both sides — useful for thin geometry like flags, sails, or single-plane decals.

### Per-object arguments

Below the type controls is a list of argument fields. The first is the **damage argument** — enter a number to bind whole-object damage (see [Lighting & Damage](04-lighting-damage.md#texture-damage)). Other fields cover material-property animations such as base colour or emissive colour shifts.

---

## Scene and object requirements

These conventions must be followed for every model. Violations typically cause silent export problems that are hard to diagnose.

### Naming and character set

All file names, materials, and textures must use **Latin characters only**. Spaces must be replaced with underscores (`_`).

This restriction applies throughout the pipeline — Blender file names, material names, texture file names, FBX intermediate exports if you use them, and the final `.edm` output.

Materials must include the **model name** as a prefix or suffix. For Eagle Dynamics' KS-19 anti-aircraft gun example, the body material is named `KS19_body`. Key objects (gun, barrel, body, hull, turret, etc.) should also carry clear, descriptive names.

See [Naming conventions reference](reference/naming-conventions.md) for the consolidated list.

### Orientation

Models must face forward along the **positive X axis**. The bow of a ship, the nose of an aircraft, the front of a vehicle all point along `+X`.

Use Blender's navigation gizmo (with X and Y axis display enabled) to verify alignment.

### Transforms

Reset all object **Scale** and **Rotation** with `Ctrl + A` (apply transforms) before exporting. Non-applied transforms produce unpredictable export results.

Pivot positions matter for animation: place pivots at the actual centre of rotation. The wheel example used in the ED tutorials places the pivot at the wheel's centre using the `Shift + S` 3D cursor workflow described in [Geometry & Animation](02-geometry-animation.md#setting-pivots).

### View layers

Use the **default view layers**. Changing them is not recommended — it can cause export errors that are hard to trace.

---

## Layer organisation

The Outliner organises a scene into layers. The exporter uses layer membership and checkbox state to decide what to export.

### Core rule: the checkbox, not the eye

What gets exported is determined by the **checkbox** in the Outliner — not the eye icon (viewport visibility) and not the monitor icon (render visibility). The eye and monitor icons affect Blender's viewport only.

### Recommended structure

```
Scene
└── Export                    ← root export layer
    ├── LOD0_50               ← LOD layers, see Geometry chapter
    │   ├── hull
    │   ├── island
    │   └── details
    ├── LOD1_150
    ├── LOD2_500
    ├── Collision             ← collision geometry with its own animation empties
    ├── BoundingBox           ← bounding/user boxes live in the root export folder
    └── Connectors            ← LOD0 only
```

For complex scenes (the ED ship example), each LOD can be split into sub-layers — hull, island, details — to keep the Outliner navigable. Collision geometry lives in its own layer alongside the LOD layers, with its own animation empties. **Bounding and user boxes must be placed in the root export folder**, not nested inside an LOD.

For details on layer-naming conventions and LOD distance specifiers, see [Geometry & Animation — LOD management](02-geometry-animation.md#lod-management).

---

## Updating the add-on

New exporter versions are released regularly with improvements and fixes. Check for updates and reinstall as required.

### Standard update method

Repeat the standard installation:

1. **Edit > Preferences > Add-ons**
2. **Install from Disk**
3. Select the new `.zip`

### Manual add-on update

If you prefer to swap the folder directly:

1. Locate the `IO_Scene_EDM` folder in your Blender add-ons directory.

   > **Tip:** the easiest way to find this folder is to click **Open Learning Demo** in the EDM settings — the demos folder sits *inside* `IO_Scene_EDM`. Navigate up one level from the Learning Demos folder to reach `IO_Scene_EDM` itself.

2. Delete the old `IO_Scene_EDM` folder.
3. Extract the new version's `IO_Scene_EDM` folder from the downloaded zip into the same location.
4. Restart Blender.

---

## Resources

- **Open Learning Demo** button (in EDM add-on settings) — opens the demo `.blend` files folder.
- [Eagle Dynamics official site](https://www.digitalcombatsimulator.com/) — for the exporter download.
- [Blender Preferences documentation](https://docs.blender.org/manual/en/latest/editors/preferences/index.html) — general reference for the Preferences window.
- [Blender Add-on management](https://docs.blender.org/manual/en/latest/editors/preferences/addons.html) — Blender's documentation on add-on installation.

---

> *Next: [Geometry and animation](02-geometry-animation.md)*
