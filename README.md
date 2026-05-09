# DCS Modding Handbook

Community handbook for building 3D models, animations, and textures for **Digital Combat Simulator** using **Blender** and the official **EDM exporter** (`IO_Scene_EDM`).

## :rocket: Read the handbook

**[https://thebgpikester.github.io/dcs-blender-modding-handbook/](https://thebgpikester.github.io/dcs-blender-modding-handbook/)**

The site has full search, dark mode, and a navigable structure. The repo is the source; the site is the rendered version.

## What's in this repo

```
docs/                   ← All handbook content (markdown source)
docs/reference/         ← Quick-lookup reference tables
docs/images/            ← Screenshots and diagrams
docs/stylesheets/       ← Custom CSS theme overrides
mkdocs.yml              ← Site configuration
requirements.txt        ← Python build dependencies
.github/workflows/      ← Auto-deploy site on push
```

## Contributing

Pull requests are welcome. See the [Contributing guide](https://thebgpikester.github.io/dcs-blender-modding-handbook/contributing/) on the site, or [`docs/contributing.md`](docs/contributing.md) in this repo.

If you find an error, [open an issue](../../issues/new/choose).

## Local development

If you want to preview the site locally before pushing changes:

```bash
# One-time setup
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# Run the preview server
mkdocs serve
# Open http://127.0.0.1:8000/ — auto-reloads on save
```

You can also push directly without local preview — the site rebuilds on every push to `main` via GitHub Actions and goes live in ~60-90 seconds.

## License

[MIT](LICENSE). Independent community project. Eagle Dynamics retains all rights to their video content, software, and trademarks. Not affiliated with or endorsed by Eagle Dynamics.
