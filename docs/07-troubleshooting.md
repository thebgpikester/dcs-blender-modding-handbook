# Troubleshooting

> **Status:** Planned. This chapter will collect common errors and their resolutions, organised by symptom.

## Categories planned

### Export-time errors

- Material version mismatch — fix via **Update EDM Materials**
- LOD layer overlap errors
- UV map mismatch errors when materials reference UV maps the object lacks
- Missing bounding box on bone-driven export

### Silent failures (no error, wrong result)

- Animation does not play in DCS — check for numbered prefix in Action name
- Animation does not play in DCS — check for identical first/last keyframe values
- Object exports with offset rotation — check that transforms were applied (`Ctrl + A`)
- Connectors invisible in lower LODs — connectors are LOD0 only by design

### Runtime errors in DCS

- Crash on model load — check for non-Latin characters in any name
- Damaged geometry has visible seams — apply Custom Split Normals or Data Transfer modifier

### FBX boundary issues (when using 3ds Max)

- *(Topics to be added — see [FBX pipeline](05-fbx-pipeline.md))*

### Substance Painter integration issues

- *(Topics to be added — see [Substance Painter](06-substance-painter.md))*

## Want to contribute?

If you've solved a tricky DCS Blender exporter problem, please add it here — the goal is to make this the first hit when someone searches for the error message they're seeing.

Open a [content addition issue](../.github/ISSUE_TEMPLATE/content_addition.md) describing:

1. The exact error message or symptom
2. The conditions under which it appears
3. The resolution

---

> *Previous: [Substance Painter (planned)](06-substance-painter.md)*
