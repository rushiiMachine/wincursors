# AGENTS.md

Cursor theme: Windows 10 style for Linux (xcursor + hyprcursor), compiled with [accurse](https://github.com/ATM-Jahid/accurse). No test suite by design — verification means running accurse and parsing/eyeballing its output.

## Commands

```sh
uv sync                                                # deps (accurse; Python >= 3.13)
rm -rf AC-theme && uv run accurse theme/metadata.toml  # compile the theme into AC-theme/
```

- System deps (Arch): `sudo pacman -S librsvg xorg-xcursorgen` — accurse shells out to `rsvg-convert` and `xcursorgen`.
- accurse **aborts if `AC-theme/` exists**; always remove it first.
- `uv run` from repo root; there is no installed package (`[tool.uv] package = false`).
- Releases: pushing a `v*` tag runs `.github/workflows/release.yml` (build → tarball/zip → GitHub release). CI deps differ from Arch: `apt-get install librsvg2-bin x11-apps` (xcursorgen lives in x11-apps there).

## Layout / ownership

- `theme/` is the source of truth: `metadata.toml` (manifest: shapes, hotspots, aliases, animation) plus one SVG per static shape and a `theme/<shape>/<shape>-NN.svg` frame folder per animated shape (`wait`, `progress`).
- `AC-theme/` is the build output (`cursors/` xcursor binaries + alias symlinks, `hyprcursors/*.hlc`, `index.theme`, `manifest.hl`) — never edit it; regenerate instead.

## Hard-won facts / gotchas

- Hotspots in `metadata.toml` are in 64×64 canvas coordinates (`shape_size = 64`); accurse scales them per output size. accurse's `check_toml` requires the config-level `x_hotspot`/`y_hotspot` to be **> 0** (32/32 is the neutral default; per-shape 0 is fine).
- accurse finds animation frames via glob `<shape>*.svg` recursively under `theme/` — a stray `<shape>.svg` file anywhere silently becomes an extra animation frame. Frames play in filename sort order (hence zero-padded `-NN`).
- `right_ptr` is a mirrored copy of `left_ptr`: its `[cursors.right_ptr]` block points at the same unflipped SVG with `mirror = 1` (config) + `flips = 1`, and stores left_ptr's hotspot — accurse mirrors both artwork and hotspot at build time.
- Alias convention (verified against Qt's `qwaylandcursor.cpp`): `size_bdiag` ↔ `nesw-resize` (/ diagonal), `size_fdiag` ↔ `nwse-resize` (\ diagonal) — counterintuitive; don't "fix" it. Hash-like symlink names (e.g. `08e8e1c9…`) are legacy X hash aliases — keep them.
- Several SVGs carry an intentional vertical-flip `<g transform="matrix(1 0 0 -1 0 32)">` wrapper from their original authoring — it is part of the artwork; don't remove it.
- `rsvg-convert -o -` writes a file literally named `-`; omit `-o` to get stdout.
- Verify builds by parsing the compiled artifacts (Xcur chunk headers in `cursors/*`, `meta.hl` inside the `.hlc` zips) and rendering contact sheets with hotspot crosshairs — not by adding tests.
