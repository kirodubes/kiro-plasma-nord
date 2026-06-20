# kiro-plasma-nord — Project Claude Instructions

## Overview
The **Nord** Plasma global theme for the Kiro Plasma edition. One package shipping three
selectable Global Themes (Nordic, Nordic Darker, Nordic Bluish) with every layer, plus
Nord's own folder icons and prebuilt cursors. Sibling to [[kiro-plasma-sweet]].

## Current state
- Source repo: `~/KIRO/kiro-plasma-nord` (payload under `usr/`, Kvantum default under `etc/skel`).
- Build recipe: `~/KIRO-PKG-BUILD-APPS/kiro-plasma-nord`.
- Two upstreams (EliverLara/Nordic `kde/` + EliverLara/Nordic-kde) — see [UPSTREAM.md](./UPSTREAM.md).

## Patterns / things to know
- **Icons are Nord's own** (deliberate choice for this theme, unlike kiro-plasma-sweet
  which uses neo-candy). They are folder-only themes inheriting `Papirus-Dark` → recipe
  depends on `papirus-icon-theme`. Don't repoint to neo-candy.
- **Plasma 6 metadata is hand-written.** The look-and-feel (×3) and desktoptheme ship
  `metadata.json` (not upstream's `metadata.desktop`). On any upstream refresh, redo the
  conversion and delete the `.desktop` — see UPSTREAM.md step 1. Without it the themes
  won't show in Plasma 6 System Settings.
- **Upstream bug to keep fixed:** `look-and-feel/Nordic/contents/defaults` upstream says
  `candy-Nordic-green` (nonexistent) → must be `Nordic-green`.
- Cursors: bundled (`Nordic-cursors`), referenced by the defaults — no edit needed.
- Kvantum default for new users: `etc/skel/.config/Kvantum/kvantum.kvconfig` → `theme=Nordic`.
- `konsole/` excluded (Kiro ships `kiro-plasma-konsole`); `kvantum/*.tar.xz` excluded.
- Mixed delivery: payload → `/usr/share`, Kvantum selection → `/etc/skel`. PKGBUILD copies both.
- **Must be tested on the Plasma test box** — the P6 metadata conversion needs visual
  confirmation that all three Global Themes appear and apply.
