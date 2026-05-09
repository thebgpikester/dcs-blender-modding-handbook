# Hotkey reference

Consolidated keyboard shortcuts for working with the EDM exporter in Blender. Hotkeys with no specific exporter context (general Blender shortcuts) are noted as such.

> All shortcuts use the default Blender keymap. If you have customised keys, consult **Edit > Preferences > Keymap**.

---

## Scene and viewport

| Hotkey | Action | Context | Notes |
|---|---|---|---|
| `N` | Toggle sidebar | 3D Viewport, Shader Editor | Reveals the EDM Export tab and other panel groups |
| `C` | Create new view layer | Outliner | Used for creating Export and LOD layers ([Geometry & Animation](../02-geometry-animation.md#layer-organisation)) |
| `Shift + S` | Snap menu (3D cursor) | 3D Viewport | Used for placing the 3D cursor before setting object origins |

## Object and transform

| Hotkey | Action | Context | Notes |
|---|---|---|---|
| `Ctrl + A` | Apply Transforms (Location, Rotation, Scale) | Object Mode | Mandatory before export |
| `Ctrl + P` | Parent objects | Object Mode | Select child first, then parent. Cursor must be over the 3D viewport |
| `Shift + Ctrl + P` | Armature deform parent | Object Mode | Used for bone-driven setups |
| `Y` | Edge Split | Edit Mode | Splits selected edges. Used before `P` (Separate) for clean geometry damage breaks |
| `P` | Separate | Edit Mode | Separates selection. Options: Selection, By Material, By Loose Parts |
| `Ctrl + J` | Join | Object Mode | Joins selected objects into the active one |

## Animation

| Hotkey | Action | Context |
|---|---|---|
| `I` | Insert Keyframe | Hover over the property to keyframe, then press |
| `Reset` (button) | Set timeline 0–200, playhead 100 | EDM Export panel |

## Shader Editor

| Hotkey | Action | Context | Notes |
|---|---|---|---|
| `Shift + A` | Add menu | Shader Editor | EDM Materials submenu lives here |
| `Ctrl + Shift + LMB` | Connect node to Material Output | Shader Editor | Requires Node Wrangler |
| `Ctrl + T` | Add Texture Setup | Shader Editor | Adds Mapping + Texture Coordinate nodes pre-wired. Requires Node Wrangler |
| `N` | Toggle sidebar | Shader Editor | Reveals the Node tab where argument indices are entered in the Label field |

## Data Transfer modifier (geometry damage)

| Hotkey | Action |
|---|---|
| `Ctrl + L` → **Copy Modifiers** | Copy modifiers from active object to all selected objects. Active must be selected last |

---

## EDM Export panel buttons

These are not keyboard shortcuts but are the primary panel-level controls.

| Button | Purpose |
|---|---|
| **Fast EDM Export** | Exports to the path of the current `.blend` file or the configured settings path |
| **Set Argument** | Isolates one animation argument by number; mutes all others |
| **Mute All** | Disables every animation on visible objects |
| **Unmute All** | Re-enables every animation on visible objects |
| **Reset** | Resets timeline to 0–200 with playhead at 100 |
| **Update Materials** | Migrates all scene materials to the current exporter-compatible version |

The standard manual export path is **File > Export > EDM**.

---

## Cross-references

- [Installation — EDM Export panel](../01-installation.md#the-edm-export-panel)
- [Geometry & Animation — Animation workflow](../02-geometry-animation.md#animation-workflow)
- [Materials & Textures — Material workflow](../03-materials-textures.md#material-workflow)
