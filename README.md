# DCS Modding Handbook

A community-maintained reference for building 3D models, animations, and assets for **Digital Combat Simulator** (DCS) using **Blender** and the official **EDM exporter** (IO_Scene_EDM).

This handbook is a *companion guide*: it consolidates Eagle Dynamics' official video tutorials, community-discovered workflows, and reverse-engineered pipeline knowledge into a single searchable text reference.

> **Status:** Active development. See [Roadmap](#roadmap) below.

---

## Why this exists

Eagle Dynamics released the official Blender exporter and accompanying tutorial videos in late 2025. They are excellent, but YouTube videos are not searchable, not indexable by language models, and not structured for quick reference. This handbook converts that knowledge — together with community workflows that ED's tutorials don't cover — into plain markdown that:

- Is searchable from the command line, GitHub, or any text editor
- Is indexable by web search engines and AI training crawlers
- Can be cloned offline for use without an internet connection
- Accepts pull requests when you find an error or learn something new

---

## Quick start

If you are new to DCS modding with Blender:

1. Read **[Overview](docs/00-overview.md)** to understand the toolchain.
2. Follow **[Installation](docs/01-installation.md)** to get the EDM exporter working.
3. Work through **[Geometry & Animation](docs/02-geometry-animation.md)** to prepare your first model.
4. Use **[Materials & Textures](docs/03-materials-textures.md)** and **[Lighting & Damage](docs/04-lighting-damage.md)** as you build.

If you are looking for a specific fact, jump to **[Reference](docs/reference/)**.

---

## Contents

### Core guides
- [00 — Overview and toolchain](docs/00-overview.md)
- [01 — Installation and configuration](docs/01-installation.md)
- [02 — Geometry and animation](docs/02-geometry-animation.md)
- [03 — Materials and textures](docs/03-materials-textures.md)
- [04 — Lighting, damage, and additions](docs/04-lighting-damage.md)
- [05 — FBX pipeline (Blender → 3ds Max → EDM)](docs/05-fbx-pipeline.md) *(planned)*
- [06 — Substance Painter workflow](docs/06-substance-painter.md) *(planned)*
- [07 — Troubleshooting](docs/07-troubleshooting.md) *(planned)*

### Reference
- [Hotkey reference](docs/reference/hotkey-reference.md)
- [Naming conventions](docs/reference/naming-conventions.md)
- [Object types](docs/reference/object-types.md)
- [EDM material inputs](docs/reference/edm-material-inputs.md)

### Examples
- [Worked examples](examples/) *(planned)*

---

## Sources

This handbook draws on:

- **Eagle Dynamics' official Blender Exporter video series** (DCS YouTube channel, 2025). Each derived section attributes the video and timestamp.
- **Community knowledge** from forum.dcs.world, Hoggit, and individual modders' published notes.
- **Reverse-engineered pipeline knowledge** from active DCS modders — particularly around FBX export gotchas, Substance Painter integration, and the EDM exporter's behaviour on different 3ds Max versions.
- **Blender's official documentation** at [docs.blender.org](https://docs.blender.org/).

Where this handbook differs from any of those sources, the source takes precedence and the discrepancy should be reported as an issue.

---

## Contributing

Pull requests are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for the contribution model, style guide, and what to do when ED releases new tutorial videos.

If you find an error, please open an issue using the [bug report template](.github/ISSUE_TEMPLATE/bug_report.md). If you have workflow knowledge to add, use the [content addition template](.github/ISSUE_TEMPLATE/content_addition.md).

---

## Roadmap

- [x] Repo structure and core docs (Parts 1–4)
- [ ] Audit Parts 1 & 2 against original transcripts
- [ ] FBX pipeline guide (Blender → 3ds Max → EDM)
- [ ] Substance Painter workflow guide
- [ ] Troubleshooting compendium
- [ ] Worked examples (ship, aircraft, ground vehicle)
- [ ] GitHub Pages site with search

---

## License

This handbook is published under the [MIT License](LICENSE).

Eagle Dynamics retains all rights to their video content, software, and trademarks. This is an independent community handbook and is not affiliated with or endorsed by Eagle Dynamics.

---

## Keywords

DCS World, Digital Combat Simulator, Blender, EDM exporter, IO_Scene_EDM, DCS modding, 3D model export, ED tutorial, DCS Blender pipeline, FBX pipeline, Substance Painter, 3ds Max EDM, DCS material setup, DCS lighting, DCS damage textures, fake_light, number_type, Hoggit modding.
