<p align="center">
  <img src="kiro.jpg" alt="Kiro" width="220" />
</p>

# kiro-plasma-nord

Kiro's **Nord** Plasma global theme — the calm, muted Nord palette, packaged as three
ready-to-pick Global Themes with cohesive Nord icons and cursors.

## What it ships

Three Global Themes you select in **System Settings → Appearance → Global Themes**:
**Nordic**, **Nordic Darker**, **Nordic Bluish**. Each pulls in its full set of layers:

| Layer | Path | Notes |
|---------------------|------------------------------------------|------------------------------|
| Plasma desktop theme | `/usr/share/plasma/desktoptheme/Nordic` | panels, widgets, popups |
| Global themes (look-and-feel) | `/usr/share/plasma/look-and-feel/Nordic{,-darker,-bluish}` | the three variants |
| Window decorations | `/usr/share/aurorae/themes/Nordic` | shared Aurorae titlebars |
| Color schemes | `/usr/share/color-schemes/*.colors` | Nordic / NordicDarker / nordicbluish |
| Kvantum themes | `/usr/share/Kvantum/Nordic*` | Qt app styling (incl. `-Solid`) |
| Kvantum default selection | `/etc/skel/.config/Kvantum/kvantum.kvconfig` | new users get `Nordic` automatically |
| SDDM login themes | `/usr/share/sddm/themes/Nordic{,-darker,-bluish}` | the login screen |
| Nord icons | `/usr/share/icons/Nordic-{green,darker,bluish}` | Nord-tinted folder icons |
| Nord cursors | `/usr/share/icons/Nordic-cursors` | prebuilt cursor theme |

The Nord folder icons inherit from **Papirus-Dark** (and fall back through Breeze), so
the package depends on `papirus-icon-theme` for the fullest icon coverage.

## Install

```bash
sudo pacman -S kiro-plasma-nord
```

Then open **System Settings → Appearance → Global Themes** and apply **Nordic**,
**Nordic Darker**, or **Nordic Bluish**. New users get the Nordic Kvantum theme for Qt
apps automatically; existing users can pick it in **Kvantum Manager**.

## Heritage

Based on [EliverLara's Nordic](https://github.com/EliverLara/Nordic) (`kde/` tree) plus
the [Nordic-kde](https://github.com/EliverLara/Nordic-kde) Plasma desktop theme. See
[UPSTREAM.md](./UPSTREAM.md) for exact sources and refresh notes. GPL-3.0 / CC BY-SA 4.0
— see [LICENSE](./LICENSE).
