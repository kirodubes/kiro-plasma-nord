# Changelog

## 2026.06.20 — Kvantum default via install scriptlet (no packaged kvconfig)

### What Changed
- Stopped shipping a packaged `kvantum.kvconfig`. The Kvantum selection is now written by a
  pacman **install scriptlet** to `/etc/skel/.config/Kvantum/kvantum.kvconfig` (`theme=Kiro-Nordic`)
  on install/upgrade — moved off the old `/etc/xdg` location so it no longer clashes with the
  other kiro-plasma themes or `kiro-kvantum`. They all coexist now; last-installed theme wins,
  falling back to `kiro-kvantum`'s `ArcDark` baseline.

### Technical Details
- Removed the `etc/` payload entirely (it held only the `/etc/xdg` Kvantum selection); the
  PKGBUILD no longer copies `etc/`. Added `install=kiro-plasma-nord.install`
  (`post_install`/`post_upgrade`) and `depends+=('kiro-kvantum')` so the overwritten baseline
  stays package-owned and the override is deterministic.

### Files Modified
- Removed `etc/xdg/Kvantum/kvantum.kvconfig` (+ empty `etc/`)
- `../KIRO-PKG-BUILD-APPS/kiro-plasma-nord/PKGBUILD` — drop `cp etc`, add `install=` + `kiro-kvantum` dep
- `../KIRO-PKG-BUILD-APPS/kiro-plasma-nord/kiro-plasma-nord.install` — new scriptlet

## 2026.06.20 — rename theme identity to Kiro namespace (coexist with upstream Nordic)

### What Changed
- Renamed every shipped theme identity from the upstream `Nordic` name into a `Kiro-`
  namespace so the package **coexists with the upstream Nordic theme** — a user can install
  `nordic-kde-theme-git` / `nordic-kvantum-git` alongside this without any pacman file
  conflict — and so System Settings shows honest **Kiro Nordic** / **Kiro Nordic Darker** /
  **Kiro Nordic Bluish** labels.

### Technical Details
- Renamed the 3 look-and-feel variants, the desktoptheme, the shared aurorae theme (+ `*rc`),
  all 6 Kvantum dirs (incl. inner `.kvconfig`/`.svg`), the 3 SDDM themes, the 3 color schemes,
  the 3 Nord folder icon themes, and the cursor theme — all `Kiro-Nordic*`.
- Cross-references repointed in each look-and-feel `contents/defaults`: `[Icons] Theme=`
  (Kiro folder icons), `cursorTheme`, `ColorScheme` (matched to each `.colors` internal
  `Name`), `plasmarc name`, `__aurorae__svg__Kiro-Nordic`. Icon `Inherits` chains point at
  external sets (Papirus-Dark etc.) — unchanged. Splash `images/Nordic.png` is a relative
  internal asset — left as-is. `etc/xdg` Kvantum default → `theme=Kiro-Nordic`.
- PKGBUILD: dropped `conflicts=(nordic-kde-theme-git nordic-kvantum-git)` and bumped `pkgrel`.

### Files Modified
- Renamed all theme dirs/files under `usr/share/{plasma,aurorae,color-schemes,Kvantum,sddm,icons}` to `Kiro-Nordic*`
- Three look-and-feel `contents/defaults` + `etc/xdg/Kvantum/kvantum.kvconfig` — repointed
- `README.md`, `CLAUDE.md` — Kiro Nordic naming + coexistence note
- `../KIRO-PKG-BUILD-APPS/kiro-plasma-nord/PKGBUILD` — drop upstream conflicts, bump pkgrel

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
- **Default Kvantum selection** shipped via `/etc/xdg/Kvantum/kvantum.kvconfig`
  (`theme=Nordic`) — the system XDG default layer, moved off `/etc/skel`. Kvantum's style
  plugin resolves its config through `QStandardPaths::standardLocations()` (full XDG
  cascade), so the `/etc/xdg` copy applies to every user including the live ISO user, not
  just newly-created accounts, and uninstalls cleanly as a package-owned file. Mirrors how
  `kiro-plasma-system-settings` ships its defaults.
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
- etc/xdg/Kvantum/kvantum.kvconfig (new — system XDG default, replaces the earlier etc/skel copy)
- README.md, CHANGELOG.md, CLAUDE.md, UPSTREAM.md, LICENSE, up.sh, setup.sh, kiro.jpg, .gitignore (new)
