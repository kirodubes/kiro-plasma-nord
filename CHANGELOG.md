# Changelog

## 2026.06.23 — Purge leftover Nord teal-green `#8fbcbb` accent across every theme layer

### What Changed
- **Fixed green selection highlight in QWidget apps (e.g. Dolphin).** The 2026.06.22 recolor
  updated the color scheme (`.colors`) but missed the matching Kvantum widget themes, so
  Kvantum-styled QWidgets still highlighted with the old Nord teal-green `#8fbcbb`. QML/Kirigami
  UIs (System Settings) already showed the new blue because they read the color scheme, not
  Kvantum — hence the mismatch.
- Set `highlight.color` and `inactive.highlight.color` to `#3e90bf` (the scheme's selection
  background `62,144,191`) in `Kiro-Nordic.kvconfig` and `Kiro-Nordic-Solid.kvconfig`.
- **Fixed the persistent green in the Plasma shell.** The desktop theme's own `colors` file
  (`desktoptheme/Kiro-Nordic/colors`, `ChangeSelectionColor=true`) still carried the green
  selection/decoration — it colors the panel, Kickoff, system tray and plasmoids independently
  of the color scheme, so the green survived every scheme change. Recolored selection →
  `62,144,191` and all `DecorationFocus`/`DecorationHover` → `61,174,233`.
- **Recolored every remaining baked-in `#8fbcbb`** in the main Kiro-Nordic theme to the blue
  accent `#3daee9`: desktop-theme widget SVGs (checkmarks, bar meters, clock), the Kvantum
  `.svg` files (focus rings / handles the `.kvconfig` doesn't cover), the look-and-feel
  greeter/splash QML + busy spinner, and the SDDM user delegate.

### Technical Details
- The Nord teal-green accent lived in **five independent layers**, each read by a different
  subsystem: color scheme `.colors` (KColorScheme → QML/Kirigami), Kvantum `.kvconfig`
  (QWidget palette), Kvantum `.svg` (QWidget control rendering), desktop-theme `colors`
  (Plasma shell), and baked hex in theme/greeter SVGs+QML. Fixing only the first two on
  2026.06.22/earlier left the shell and SVG layers green — hence "persistent."
- The Darker variant (`Kiro-NordicDarker.colors`, `Kiro-Nordic-darker` splash) uses a separate
  slate-gray accent `#4c566a` and was left as-is; it still has two stray `143,188,187` leftovers
  (a `ForegroundVisited` link color and its splash spinner) flagged for a separate decision.

### Files Modified
- `usr/share/Kvantum/Kiro-Nordic/Kiro-Nordic.kvconfig`, `usr/share/Kvantum/Kiro-Nordic-Solid/Kiro-Nordic-Solid.kvconfig`
- `usr/share/Kvantum/Kiro-Nordic/Kiro-Nordic.svg`, `usr/share/Kvantum/Kiro-Nordic-Solid/Kiro-Nordic-Solid.svg`
- `usr/share/plasma/desktoptheme/Kiro-Nordic/colors`
- `usr/share/plasma/desktoptheme/Kiro-Nordic/widgets/{checkmarks,bar_meter_horizontal,bar_meter_vertical,clock}.svg`
- `usr/share/plasma/look-and-feel/Kiro-Nordic/contents/components/{UserDelegate,ActionButton}.qml`
- `usr/share/plasma/look-and-feel/Kiro-Nordic/contents/splash/Splash.qml`, `.../splash/images/busy.svg`
- `usr/share/sddm/themes/Kiro-Nordic/components/UserDelegate.qml`

## 2026.06.22 — Switch icons to Surfn-Mint-Y-Aqua; drop bundled Nord icons; remove Bluish variant

### What Changed
- **Icons → `Surfn-Mint-Y-Aqua`** for every variant, instead of the per-variant
  `Kiro-Nordic-*` folder icons. Added `surfn-mint-y-icons-git` (in `nemesis_repo`) as a
  dependency so the icons are present on install.
- **Slimmed the package:** removed the bundled `Kiro-Nordic-{green,bluish,darker}` icon
  themes (no longer used) and dropped the `papirus-icon-theme` dependency they relied on.
  Only `Kiro-Nordic-cursors` remains under `usr/share/icons`.
- **Removed the Nordic Bluish variant** entirely — the package now ships two Global Themes
  (Kiro Nordic, Kiro Nordic Darker). Deleted the bluish look-and-feel, color scheme, both
  Kvantum themes, and SDDM theme. Desktop theme and Aurorae are shared `Kiro-Nordic` and
  were untouched.
- **Recolored the Kiro-Nordic accent** away from the Nord teal-green `143,188,187` (#8FBCBB).
  `Kiro-Nordic.colors` was rebuilt to capture the tuned live look from the test box: a blue
  accent `61,174,233` (#3DAEE9) on `DecorationFocus`/`DecorationHover` across every section,
  selection background `62,144,191` (#3E90BF) with black text, and link color `183,220,251`.
  Kiro-NordicDarker uses a separate accent and was left as-is.

### Technical Details
- Changed `[kdeglobals][Icons] Theme=` in the `contents/defaults` of the remaining look-and-feel
  variants to `Surfn-Mint-Y-Aqua`.
- PKGBUILD: `depends` now `('surfn-mint-y-icons-git' 'kvantum' 'sddm' 'kiro-kvantum')` —
  added the Surfn icons, removed `papirus-icon-theme`. `pkgdesc` now reads "2 variants
  (Nordic, Darker)". `pkgrel` auto-bumped by `build-data.sh`.
- Bluish removal deleted: `look-and-feel/Kiro-Nordic-bluish`,
  `color-schemes/Kiro-nordicbluish.colors`, `Kvantum/Kiro-Nordic-bluish{,-solid}`,
  `sddm/themes/Kiro-Nordic-bluish`. The blanket `cp -a usr` install means the deletions
  drop straight out of the package.

### Files Modified
- `usr/share/plasma/look-and-feel/Kiro-Nordic/contents/defaults`
- `usr/share/plasma/look-and-feel/Kiro-Nordic-darker/contents/defaults`
- Removed: `usr/share/icons/Kiro-Nordic-{green,bluish,darker}/`
- Removed (Bluish): `usr/share/plasma/look-and-feel/Kiro-Nordic-bluish/`,
  `usr/share/color-schemes/Kiro-nordicbluish.colors`,
  `usr/share/Kvantum/Kiro-Nordic-bluish/`, `usr/share/Kvantum/Kiro-Nordic-bluish-solid/`,
  `usr/share/sddm/themes/Kiro-Nordic-bluish/`
- `README.md`, `CLAUDE.md`, `UPSTREAM.md`
- `../KIRO-PKG-BUILD-APPS/kiro-plasma-nord/PKGBUILD`

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
