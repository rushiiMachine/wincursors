# wincursors

A Windows 10 style cursor theme for Linux, built with
[accurse](https://github.com/ATM-Jahid/accurse). One set of SVG assets is
compiled into both the classic **xcursor** format and the newer
**hyprcursor** format (Hyprland), including all the usual size variants
and shape aliases.

**WARNING**: This theme was compiled together almost entirely with the help of
AI. However, all cursor assets came from existing sources, and this project
contains no generated assets, nor code to maintain.

## Installation

Download `wincursors.zip` from the [latest release](../../releases/latest)
and extract it into your icon directory:

```sh
mkdir -p ~/.local/share/icons
curl -fLo /tmp/wincursors.zip https://github.com/rushiiMachine/wincursors/releases/latest/download/wincursors.zip
unzip -o /tmp/wincursors.zip -d ~/.local/share/icons
```

This installs the theme as `~/.local/share/icons/wincursors`. Then enable it
for your desktop environment:

### GNOME

```sh
gsettings set org.gnome.desktop.interface cursor-theme 'wincursors'
gsettings set org.gnome.desktop.interface cursor-size 32
```

Or use GNOME Tweaks → Appearance → Cursor. Changes apply immediately for
native GNOME apps; re-login if some (usually X11/XWayland) apps keep the old
theme.

### KDE Plasma

System Settings → Colors & Themes → Cursors (on older Plasma releases:
Appearance → Cursor Theme) and pick **wincursors**, or from a terminal:

```sh
plasma-apply-cursortheme wincursors
```

### Hyprland

Add the following to `hyprland.conf`:

```ini
env = XCURSOR_THEME,wincursors
env = XCURSOR_SIZE,32
```

Hyprland picks up the bundled hyprcursor theme automatically; the environment
variables additionally cover XWayland applications. `hyprctl setcursor
wincursors 32` changes the cursor at runtime without reloading the config.

### Anything else

Most other environments respect the `XCURSOR_THEME` environment variable. For
stray applications that ignore both it and your desktop settings, create
`~/.icons/default/index.theme` containing:

```ini
[Icon Theme]
Inherits=wincursors
```

You should test that most cursors are correctly displaying by
viewing the following cursor test sites:
- https://vibhorjaiswal.github.io/Cursor-Test
- https://codepen.io/WebDevCA/pen/DRjdMa

## Building

You need [uv](https://docs.astral.sh/uv/), `rsvg-convert` and
`xcursorgen` on your `PATH`. On Arch Linux:

```sh
sudo pacman -S librsvg xorg-xcursorgen
```

(On Debian/Ubuntu the equivalents are `librsvg2-bin` and `x11-apps`.)

Then, from the repository root:

```sh
uv sync
rm -rf AC-theme
uv run accurse theme/metadata.toml
```

The finished theme is written to `AC-theme/`. Pushing a `v*` tag runs the
release workflow, which builds the theme and attaches the packages to a
GitHub release.

## Repository layout

```
theme/       cursor manifest (metadata.toml) and SVG sources — edit here
AC-theme/    compiled theme (generated, not checked in)
```

`theme/metadata.toml` defines every cursor shape: its hotspot, the X/CSS
names it should be reachable under, and animation timing for the animated
shapes (`wait`, `progress`), whose frames live in `theme/wait/` and
`theme/progress/`.

## Credits

Cursor design follows the Windows 10 cursors, as well as [We10XOS](https://github.com/yeyushengfan258/We10XOS-cursors) cursors.
