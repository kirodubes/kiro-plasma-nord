# Upstream references — kiro-plasma-nord

Everything needed to refresh this theme from upstream in the future.

## Sources (TWO upstreams)

| Component | Repo | Branch | Notes |
|-----------------------|------------------------------------------|--------|-----------------------|
| Most layers | https://github.com/EliverLara/Nordic | `master` | the `kde/` subfolder |
| Plasma desktop theme | https://github.com/EliverLara/Nordic-kde | `master` | the Plasma style |

- **Original author:** EliverLara
- **License:** GPL-3.0 (Nordic) + CC BY-SA 4.0 (the desktop theme); upstream `LICENSE` carried into this repo.
- Vendored from `EliverLara/Nordic` @ `master` (last upstream commit 2026-05-04) and `EliverLara/Nordic-kde` @ `master`. Record the exact commit hashes here on each refresh.

## What was copied (upstream path → install path)

From `EliverLara/Nordic/kde/`:
| Upstream | Installed to |
|------------------------------|----------------------------------------|
| `plasma/look-and-feel/{Nordic,Nordic-darker,Nordic-bluish}` | `usr/share/plasma/look-and-feel/` |
| `aurorae/Nordic` | `usr/share/aurorae/themes/Nordic` |
| `colorschemes/*.colors` | `usr/share/color-schemes/` |
| `kvantum/*` (dirs only, not the `.tar.xz`) | `usr/share/Kvantum/` |
| `sddm/{Nordic,Nordic-darker,Nordic-bluish}` | `usr/share/sddm/themes/` |
| `folders/{Nordic-green,Nordic-darker,Nordic-bluish}` | `usr/share/icons/` |
| `cursors/Nordic-cursors` (prebuilt) | `usr/share/icons/Nordic-cursors` |

From `EliverLara/Nordic-kde`:
| Upstream | Installed to |
|------------------------------|----------------------------------------|
| `colors`,`dialogs`,`icons`,`widgets` | `usr/share/plasma/desktoptheme/Nordic/` |

## Intentionally NOT copied

- `konsole/` — Kiro ships `kiro-plasma-konsole` separately.
- `kvantum/*.tar.xz` — redundant archives of the dirs we ship.
- The GTK / gnome-shell / xfce trees in the main Nordic repo — not KDE.

## Kiro edits to re-apply after any refresh

1. **Plasma-6 metadata.** Upstream ships Plasma-5 `metadata.desktop` (PluginInfo 0.1).
   For each look-and-feel variant and the desktoptheme, write a Plasma-6 `metadata.json`
   (`KPackageStructure`, `KPlugin.Id`/`Name`, `X-Plasma-API-Minimum-Version: "6.0"` for
   look-and-feel) and **delete the `metadata.desktop`**. Without this the Global Themes
   do not appear in Plasma 6 System Settings. (See the existing `metadata.json` files for
   the exact shape.)
2. **Upstream icon-name bug.** `look-and-feel/Nordic/contents/defaults` references a
   non-existent `candy-Nordic-green`; fix it to `Theme=Nordic-green` (the real folder
   theme shipped in `icons/`).

Kiro-only files (not from upstream — leave as is on refresh):
- `etc/skel/.config/Kvantum/kvantum.kvconfig` → `[General] theme=Nordic`

## Verify on the Plasma test box

The Plasma-6 metadata conversion must be confirmed visually: the three Global Themes
should appear and apply in System Settings, with the Nord icons/cursors/Kvantum active.
