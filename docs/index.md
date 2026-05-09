---
title: Home
description: Community handbook for building 3D models, animations, and textures for Digital Combat Simulator using Blender and the EDM exporter.
hide:
  - navigation
---

# DCS Modding Handbook

A community-maintained reference for building 3D models, animations, and textures for **Digital Combat Simulator** using **Blender** and the official **EDM exporter** (`IO_Scene_EDM`).

This handbook is a *companion guide* — it consolidates Eagle Dynamics' official video tutorials, community-discovered workflows, and reverse-engineered pipeline knowledge into a single searchable text reference.

!!! info "Status"
    Active development. See the [Roadmap](#roadmap) below for current priorities.

---

## Quick start

If you are new to DCS modding with Blender, work through the guide in order:

<div class="grid cards" markdown>

-   :material-information-outline:{ .lg .middle } **[Overview](00-overview.md)**

    ---

    Toolchain, scope, and what this handbook covers.

-   :material-download:{ .lg .middle } **[Installation](01-installation.md)**

    ---

    Get the EDM exporter installed and Blender configured.

-   :material-cube-outline:{ .lg .middle } **[Geometry & Animation](02-geometry-animation.md)**

    ---

    Prepare your first model — pivots, LODs, animation arguments, bones.

-   :material-palette-outline:{ .lg .middle } **[Materials & Textures](03-materials-textures.md)**

    ---

    The EDM shader system, decals, light maps, deck and glass materials.

-   :material-lightbulb-on-outline:{ .lg .middle } **[Lighting & Damage](04-lighting-damage.md)**

    ---

    Lamps, fake light points, the damage system, hull number decals.

-   :material-bookmark-multiple-outline:{ .lg .middle } **[Reference](reference/hotkey-reference.md)**

    ---

    Quick-lookup tables: hotkeys, naming conventions, object types, material inputs.

</div>

---

## Why this exists

Eagle Dynamics released the official Blender exporter and accompanying tutorial videos in late 2025. They are excellent, but YouTube videos are not searchable, not indexable by language models, and not structured for quick reference.

This handbook converts that knowledge — together with community workflows that ED's tutorials don't cover — into plain markdown that:

- Is searchable from the command line, GitHub, this site, or any text editor
- Is indexable by web search engines and AI training crawlers
- Can be cloned offline for use without an internet connection
- Accepts pull requests when you find an error or learn something new

---

## Sources

This handbook draws on:

- **Eagle Dynamics' official Blender Exporter video series** (DCS YouTube channel, 2025). Each derived section attributes the video and timestamp where applicable.
- **Community knowledge** from forum.dcs.world, Hoggit, and individual modders' published notes.
- **Reverse-engineered pipeline knowledge** from active DCS modders — particularly around FBX export gotchas, Substance Painter integration, and the EDM exporter's behaviour on different 3ds Max versions.
- **Blender's official documentation** at [docs.blender.org](https://docs.blender.org/).

Where this handbook differs from any of those sources, the source takes precedence and the discrepancy should be reported as an issue.

---

## Roadmap

- [x] Repo structure and core docs (Parts 1–4)
- [x] Audit Parts 1 & 2 against original transcripts
- [x] MkDocs Material site with search and image support
- [ ] FBX pipeline guide (Blender → 3ds Max → EDM)
- [ ] Substance Painter workflow guide
- [ ] Troubleshooting compendium
- [ ] Worked examples (ship, aircraft, ground vehicle)
- [ ] Screenshots and diagrams across all chapters

---

## Contributing

Pull requests are welcome. See [Contributing](contributing.md) for the contribution model, style guide, and what to do when ED releases new tutorial videos.

If you find an error, please [open an issue](https://github.com/thebgpikester/dcs-blender-modding-handbook/issues/new/choose).

---

## License

This handbook is published under the [MIT License](https://github.com/thebgpikester/dcs-blender-modding-handbook/blob/main/LICENSE).

Eagle Dynamics retains all rights to their video content, software, and trademarks. This is an independent community handbook and is not affiliated with or endorsed by Eagle Dynamics.
