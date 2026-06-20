# Changelog

## 2026.06.20 — initial package

### What Changed
- New repo: **kiro-plasma-nord**, the **Nord** Plasma global theme for the Kiro Plasma
  edition (Nord was #4 most-downloaded global theme on the KDE Store). Ships **three
  variants** as selectable Global Themes: Nordic, Nordic Darker, Nordic Bluish.
- Combined every Nord layer into one package: Plasma desktop theme + 3 look-and-feel
  variants + Aurorae + 3 color schemes + Kvantum (incl. `-Solid`) + 3 SDDM themes, plus
  **Nord's own folder icons** (`Nordic-green/-darker/-bluish`) and **prebuilt Nord cursors**.
- **Plasma-6 metadata migration:** upstream ships Plasma-5 `metadata.desktop`; wrote
  Plasma-6 `metadata.json` for the desktoptheme and all three look-and-feel variants
  (and removed the `.desktop`), so the Global Themes appear in Plasma 6 System Settings.
- **Fixed upstream bug:** the Nordic variant's `defaults` referenced a non-existent
  `candy-Nordic-green` icon theme → corrected to `Nordic-green`.
- **Default Kvantum selection** shipped via `/etc/skel/.config/Kvantum/kvantum.kvconfig`
  (`theme=Nordic`).
- Cursors: the look-and-feel defaults already reference `Nordic-cursors`, which we bundle
  — no cursor edit needed.

### Technical Details
- Two upstreams: [EliverLara/Nordic](https://github.com/EliverLara/Nordic) (`kde/` tree)
  and [EliverLara/Nordic-kde](https://github.com/EliverLara/Nordic-kde) (desktoptheme).
  Full mapping in [UPSTREAM.md](./UPSTREAM.md).
- Nord folder icons inherit Papirus-Dark → recipe `depends` on `papirus-icon-theme`
  (plus `kvantum`, `sddm`).
- License: GPL-3.0 (Nordic) + CC BY-SA 4.0 (desktoptheme).

### Files Modified
- usr/share/plasma/desktoptheme/Nordic (new, P6 metadata.json)
- usr/share/plasma/look-and-feel/{Nordic,Nordic-darker,Nordic-bluish} (new, P6 metadata.json)
- usr/share/aurorae/themes/Nordic, color-schemes/*.colors, Kvantum/Nordic*, sddm/themes/Nordic* (new)
- usr/share/icons/{Nordic-green,Nordic-darker,Nordic-bluish,Nordic-cursors} (new)
- etc/skel/.config/Kvantum/kvantum.kvconfig (new)
- README.md, CHANGELOG.md, CLAUDE.md, UPSTREAM.md, LICENSE, up.sh, setup.sh, kiro.jpg, .gitignore (new)
