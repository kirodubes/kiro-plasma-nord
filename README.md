<p align="center">
  <img src="kiro.jpg" alt="Kiro" width="220" />
</p>

# kiro-plasma-nord

Kiro's **Kiro Nord** Plasma global theme — the calm, muted Nord palette, packaged as two
ready-to-pick Global Themes with Surfn-Mint-Y-Aqua icons and Nord cursors.

Everything ships under a `Kiro-` namespace, so it **coexists with the upstream Nordic
theme** — install the real `nordic-kde-theme-git` / `nordic-kvantum-git` alongside it
without any file conflict.

## What it ships

Two Global Themes you select in **System Settings → Appearance → Global Themes**:
**Kiro Nordic** and **Kiro Nordic Darker**. Each pulls in its full set of layers:

| Layer | Path | Notes |
|---------------------|------------------------------------------|------------------------------|
| Plasma desktop theme | `/usr/share/plasma/desktoptheme/Kiro-Nordic` | panels, widgets, popups |
| Global themes (look-and-feel) | `/usr/share/plasma/look-and-feel/Kiro-Nordic{,-darker}` | the two variants |
| Window decorations | `/usr/share/aurorae/themes/Kiro-Nordic` | shared Aurorae titlebars |
| Color schemes | `/usr/share/color-schemes/Kiro-*.colors` | Kiro-Nordic / Kiro-NordicDarker |
| Kvantum themes | `/usr/share/Kvantum/Kiro-Nordic*` | Qt app styling (incl. `-Solid`) |
| Kvantum default selection | `/etc/xdg/Kvantum/kvantum.kvconfig` | new users get `Kiro-Nordic` automatically |
| SDDM login themes | `/usr/share/sddm/themes/Kiro-Nordic{,-darker}` | the login screen |
| Icons | `Surfn-Mint-Y-Aqua` (dep `surfn-mint-y-icons-git`) | both variants use it |
| Nord cursors | `/usr/share/icons/Kiro-Nordic-cursors` | prebuilt cursor theme |

Both variants use the **Surfn-Mint-Y-Aqua** icon theme, pulled in via the
`surfn-mint-y-icons-git` dependency. The package no longer ships its own Nord folder icons.

## Install

```bash
sudo pacman -S kiro-plasma-nord
```

Then open **System Settings → Appearance → Global Themes** and apply **Kiro Nordic** or
**Kiro Nordic Darker**. New users get the Kiro Nordic Kvantum theme for Qt apps
automatically; existing users can pick it in **Kvantum Manager**.

## Heritage

Based on [EliverLara's Nordic](https://github.com/EliverLara/Nordic) (`kde/` tree) plus
the [Nordic-kde](https://github.com/EliverLara/Nordic-kde) Plasma desktop theme. See
[UPSTREAM.md](./UPSTREAM.md) for exact sources and refresh notes. GPL-3.0 / CC BY-SA 4.0
— see [LICENSE](./LICENSE).
