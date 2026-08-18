# webapp-launcher

Turn any website into a standalone dock app on Chrome/Chromium (or Brave, Edge, Vivaldi) + GNOME.

Each site gets its own `.desktop` launcher, icon, and isolated browser profile, so you stay logged in and the window groups under its own dock icon instead of Chrome.

```bash
./webapp-launcher create --name WhatsApp --slug whatsapp \
  --url https://web.whatsapp.com/
```

Then: log in, right-click the dock icon, **Add to Favorites**.

## Why StartupWMClass is the whole game

GNOME groups windows by `StartupWMClass`. Chromium-based `--app` windows set that class from the URL host (e.g. `chrome-web.whatsapp.com__-Default`). The exact string is not something you should invent.

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
./webapp-launcher remove notion   # asks before deleting login data
```

## What it writes

| Path | Purpose |
|------|---------|
| `~/.local/share/applications/<slug>.desktop` | Launcher |
| `~/.local/share/icons/<slug>.<ext>` | Icon (favicon, or `--icon`) |
| `~/.config/chrome-<slug>/` | Isolated browser profile (login session) |

`remove` deletes the launcher and icon, then asks before touching the profile.

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

**Broken icon.** Pass `--icon /path/to/icon.png` on create, or replace `~/.local/share/icons/<slug>.png`.

**Fresh login.** Close the app, delete `~/.config/chrome-<slug>/`, launch again.

## AI assistant

If you want a coding assistant to install and verify this on a machine, paste [assistant-prompt.md](assistant-prompt.md).
