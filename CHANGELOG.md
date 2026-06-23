# Changelog

## 2026.06.23 — Drop the redundant "Darker" Global Theme; keep it as a colour scheme

### What Changed
- Removed the **Kiro-Nordic-Darker look-and-feel** (`usr/share/plasma/look-and-feel/Kiro-Nordic-darker/`,
  34 files). Plasma's Global-Theme apply never applies a theme's colour scheme, so selecting the
  "Kiro Nordic Darker" Global Theme left apps light — it only differed from Kiro-Nordic by a colour
  scheme it couldn't actually apply, so as a Global Theme it was misleading.
- **Darker is now a colour scheme**, not a global theme: users who want dark apps select
  `Kiro-Nordic-Darker` in System Settings → Colours. The `Kiro-NordicDarker.colors` scheme, the
  `Kiro-Nordic-Darker(-Solid)` Kvantum themes, and the `Kiro-Nordic-darker` SDDM theme all remain
  as selectable dark-Nordic assets.
- The package now ships a single Global Theme (Kiro-Nordic); description updated.

## 2026.06.23 — Look-and-feel default widget style → Breeze (was Kvantum)

### What Changed
- Both look-and-feel `defaults` (Kiro-Nordic and Kiro-Nordic-darker) now set
  `[kdeglobals][KDE]widgetStyle=Breeze` instead of `kvantum`. Applying the Kiro-Nordic Global
  Theme was flipping the Application Style to Kvantum, which made QWidget apps (Dolphin) dark and
  **translucent** — not the intended default. Breeze is opaque and follows the colour scheme, so
  applying the theme now keeps the light, opaque default look (matching `kiro-plasma-sweet`).
- The Kvantum themes still ship under `usr/share/Kvantum/`; users who want the translucent look
  can select Kvantum in System Settings → Application Style. It's just no longer the default.

### Technical Details
- This makes the package consistent with the shipped default appearance
  (`kiro-plasma-system-settings` seeds `widgetStyle=Breeze` via `/etc/skel`); previously the LnF
  default and the seeded default disagreed, so re-applying the theme broke the look.
- Note: the LnF `defaults` still set `ColorScheme=Kiro-Nordic` / `Kiro-NordicDarker` (dark). Plasma
  does not apply a theme's colour scheme on Global-Theme apply, so this is inert in practice — the
  user keeps their seeded (light) colours. Left as-is pending a decision on the default colour scheme.

## 2026.06.23 — Move the default selector out of this package (supersedes the seed below)

### What Changed
- Removed the `etc/xdg/kdeglobals` seed and the `conflicts=('kiro-plasma-sweet')` that the
  earlier entry added (PKGBUILD no longer copies `etc/`). Reason: KIB installs **all** theme
  packages onto one system, so a default selector in a theme package would be a pacman file
  conflict (two themes both owning `/etc/xdg/kdeglobals`) and the `conflicts=` would block the
  all-themes install outright.
- The single system-wide default selector now lives in **kiro-plasma-system-settings**, which
  sets the ISO default to **Kiro-Nordic** (`LookAndFeelPackage` + `ColorScheme` + Nordic colours)
  and `depends=('kiro-plasma-nord')`. This package is back to shipping only its uniquely-named
  assets under `usr/` — it coexists with every other Kiro Plasma theme with no conflicts.
- Net effect for users is unchanged: a fresh Kiro Plasma install still boots Nordic by default;
  the default is just owned by the base settings package instead of the theme.

### Files Modified
- `etc/xdg/kdeglobals` (removed)
- `PKGBUILD` (recipe repo): dropped `conflicts=` and the `etc/` copy

## 2026.06.23 — Seed the Nordic color scheme as a system default (`/etc/xdg/kdeglobals`)

### What Changed
- **Fresh installs / new users now get the Nordic colors by default.** Plasma's Global-Theme
  apply (GUI and CLI) does **not** apply a look-and-feel's color scheme — it sets the icons,
  widget style, cursor, plasma theme and kwin decoration from the theme `defaults`, but
  deliberately preserves the existing color scheme. So selecting "Kiro Nordic" never pulled in
  its colors; the desktop stayed on whatever scheme (Breeze) was already set.
- Fixed the same way the sibling `kiro-plasma-sweet` already does it: ship the color scheme as
  the system-wide default via `etc/xdg/kdeglobals` (a copy of `Kiro-Nordic.colors`, with
  `[General]ColorScheme=Kiro-Nordic`). Apps read this through the XDG cascade when the user has
  no `~/.config` override, so a fresh Kiro boots with Nordic colors — and it covers the live-ISO
  user too, not just new accounts.
- PKGBUILD `package()` now copies `etc/` alongside `usr/` (matching Sweet).

### Technical Details
- Verified on the test box: applying the Kiro Nordic (Darker) Global Theme set
  `LookAndFeelPackage` + wrote `Icons`/`widgetStyle` to `~/.config/kdedefaults/kdeglobals`, but
  left `ColorScheme` untouched (stayed `BreezeLight`) — confirming Plasma never applies the
  scheme on theme selection. This is a Plasma behavior, not a packaging bug; seeding the default
  is the only reliable fix and is the established ecosystem pattern.
- Only one Kiro Plasma theme is ever installed, so `etc/xdg/kdeglobals` does not conflict with
  Sweet's identically-pathed file. Made this explicit with `conflicts=('kiro-plasma-sweet')` in
  the PKGBUILD (Sweet is the only other theme shipping that path), so pacman cleanly offers to
  replace rather than erroring on a file collision.

### Files Modified
- `etc/xdg/kdeglobals` (new)
- `PKGBUILD` (recipe repo): `package()` copies `etc/`

## 2026.06.23 — Fix Darker Global Theme not applying its color scheme (name mismatch)

### What Changed
- **Selecting the "Kiro Nordic Darker" Global Theme now applies its colors.** The look-and-feel
  `defaults` referenced `ColorScheme=Kiro-Nordic-Darker` (hyphenated), but the scheme file is
  `Kiro-NordicDarker.colors` so Plasma's registered id is `Kiro-NordicDarker`. The hyphenated
  name doesn't resolve (`plasma-apply-colorscheme Kiro-Nordic-Darker` → "Could not find theme"),
  so the Darker theme applied with no/Breeze colors. Pointed the `defaults` at the real id.
- The main Nordic theme was unaffected because its reference `Kiro-Nordic` matches its id exactly
  — confirming the GUI Global-Theme apply *does* apply colors when the scheme name resolves.

### Technical Details
- Plasma identifies color schemes by **filename stem**, not the `.colors` `Name=` field (which is
  display-only). The fix is the minimal one-line change to the id reference; the file was not
  renamed, so existing `Kiro-NordicDarker` applications keep working.

### Files Modified
- `usr/share/plasma/look-and-feel/Kiro-Nordic-darker/contents/defaults`

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
- The Darker variant uses a separate slate-gray accent `#4c566a` (`76,86,106`). Its two stray
  green leftovers were also cleaned: `Kiro-NordicDarker.colors` `[Colors:Window]ForegroundVisited`
  → `82,148,226` (the visited-blue it already uses elsewhere), and its splash spinner
  (`Kiro-Nordic-darker/.../busy.svg`) → the slate accent `76,86,106`. No green remains in the
  package outside this CHANGELOG.

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
