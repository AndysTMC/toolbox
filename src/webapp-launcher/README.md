# webapp-launcher

Turn any website into a standalone dock app on Chrome/Chromium (or Brave, Edge, Vivaldi) + GNOME.

Each site gets its own `.desktop` launcher, icon, and isolated browser profile, so you stay logged in and the window groups under its own dock icon instead of Chrome.

```bash
./webapp-launcher create --name WhatsApp --slug whatsapp \
  --url https://web.whatsapp.com/
```

Then: log in, right-click the dock icon, **Add to Favorites**. Launchers start maximized (`--start-maximized`).

The first open of a new profile often shows a Chrome dialog asking to set this browser as the default. **Dismiss it.** Accepting would fight your real default browser and is easy to click through by habit.

## Why StartupWMClass is the whole game

GNOME groups windows by `StartupWMClass`. Chromium-based `--app` windows set that class from the URL **host and path** (e.g. `chrome-web.whatsapp.com__-Default`, or `chrome-www.google.com__finance_-Default` for `/finance/`). The exact string is not something you should invent.

This script:

1. Writes a launcher immediately so the app is usable.
2. Opens the site (or reuses the window if it is already open).
3. Reads `WM_CLASS` from the real window with `xdotool` + `xprop`.
4. Writes that confirmed value back into the `.desktop` file.

It matches on the window's **instance** containing the site host — not the window title. Title matching fails when a site sets `document.title`, and can also match a normal Chrome tab whose title happens to contain the host (instance `chrome`), which would silently regroup the app under Chrome.

If auto-detection cannot run (missing tools, pure Wayland with no XWayland, timeout), the script keeps the usual `<browser>-<host>__-Default` value and prints GNOME Looking Glass steps. It does not pretend that value was confirmed.

## Requirements

- A Chromium-based browser on `PATH`
- GNOME (dash / favorites / Looking Glass)
- Optional, for auto-detection: `xdotool`, `xprop` (`sudo apt-get install xdotool x11-utils`) and an X11 or XWayland display (`$DISPLAY`)
- Optional, so icons are not stretched: ImageMagick (`sudo apt-get install imagemagick`) — trims empty margins, then a square canvas with 5% transparent padding on each side (no resize)

## Install

```bash
chmod +x webapp-launcher
# optional: put it on PATH
ln -s "$PWD/webapp-launcher" ~/.local/bin/webapp-launcher
```

## Usage

```
webapp-launcher create [--name NAME] [--slug SLUG] [--url URL]
                       [--icon PATH] [--browser BIN]
                       [--categories STR] [--no-detect]
webapp-launcher list
webapp-launcher remove SLUG
webapp-launcher fix-wmclass SLUG
webapp-launcher square-icon SLUG|--all
```

Flags left out of `create` are prompted for, but only on a real terminal. Piped or CI runs error instead of reading the next line of stdin as an answer.

```bash
# fully interactive
./webapp-launcher create

# skip window-class detection (pure Wayland, or you will set it yourself)
./webapp-launcher create --no-detect --name Notion --url https://www.notion.so/

# after a browser update, if the dock shows two icons
./webapp-launcher fix-wmclass notion

./webapp-launcher list
./webapp-launcher square-icon --all   # trim + 5% pad existing icons
./webapp-launcher remove notion        # asks before deleting login data
```

GNOME stretches non-square icons in the dock and the app grid. On `create` (and `square-icon`), a raster icon is prepared **without resampling**:

1. Crop empty/transparent margins down to the content (`-trim`, lossless).
2. Center that on a square canvas of `max(w,h) × 1.10` — 5% breathing room on every side.

PNG/WebP keep alpha; JPEG uses white. Running `square-icon` again is safe: it re-trims the pad and puts the same 5% back.

A snapshot of a real GNOME set (desktop files + padded icons) is in [examples/](examples/).

## What it writes

| Path | Purpose |
|------|---------|
| `~/.local/share/applications/<slug>.desktop` | Launcher |
| `~/.local/share/icons/<slug>.<ext>` | Icon (favicon, or `--icon`) |
| `~/.config/chrome-<slug>/` | Isolated browser profile (login session) |

`remove` deletes the launcher and icon, then asks before touching the profile.

Each profile is a fresh Chrome. On first launch it may ask to become the default browser — always decline.

## Troubleshooting

**Two icons in the dock.** The window class drifted (browser update) or detection never ran. Launch the app, then:

```bash
./webapp-launcher fix-wmclass <slug>
```

Or set it yourself: `Alt+F2` → `lg` → Windows tab → copy `wmclass` into `StartupWMClass=` in the `.desktop` file.

**App will not launch.** Check the `Exec=` line for a real browser path and a quoted `--app=` URL:

```bash
desktop-file-validate ~/.local/share/applications/<slug>.desktop
gtk-launch <slug> 2>&1 || gio launch ~/.local/share/applications/<slug>.desktop
```

**Broken or stretched icon.** Pass `--icon /path/to/icon.png` on create, or replace `~/.local/share/icons/<slug>.png`. If a wide/tall logo looks squashed, run `./webapp-launcher square-icon <slug>` (needs ImageMagick).

**Fresh login.** Close the app, delete `~/.config/chrome-<slug>/`, launch again.

## AI assistant

If you want a coding assistant to install and verify this on a machine, paste [assistant-prompt.md](assistant-prompt.md).
