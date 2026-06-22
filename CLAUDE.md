# kiro-plasma-nord — Project Claude Instructions

## Overview
The **Nord** Plasma global theme for the Kiro Plasma edition. One package shipping three
selectable Global Themes (Nordic, Nordic Darker, Nordic Bluish) with every layer, plus
Nord's own folder icons and prebuilt cursors. Sibling to [[kiro-plasma-sweet]].

## Current state
- Source repo: `~/KIRO/kiro-plasma-nord` (payload under `usr/`, Kvantum default under `etc/xdg`).
- Build recipe: `~/KIRO-PKG-BUILD-APPS/kiro-plasma-nord`.
- Two upstreams (EliverLara/Nordic `kde/` + EliverLara/Nordic-kde) — see [UPSTREAM.md](./UPSTREAM.md).

## Patterns / things to know
- **Icons are `Surfn-Mint-Y-Aqua`** (all three variants, set 2026.06.22) → recipe depends on
  `surfn-mint-y-icons-git` (in `nemesis_repo`). The package's own Nord folder-icon themes
  (`Kiro-Nordic-{green,bluish,darker}`) were dropped 2026.06.22, and the `papirus-icon-theme`
  dep removed with them. Only `Kiro-Nordic-cursors` remains under `usr/share/icons`.
  Don't repoint to neo-candy.
- **Plasma 6 metadata is hand-written.** The look-and-feel (×3) and desktoptheme ship
  `metadata.json` (not upstream's `metadata.desktop`). On any upstream refresh, redo the
  conversion and delete the `.desktop` — see UPSTREAM.md step 1. Without it the themes
  won't show in Plasma 6 System Settings.
- **Upstream bug to keep fixed:** `look-and-feel/Kiro-Nordic/contents/defaults` upstream says
  `candy-Nordic-green` (nonexistent) → must be `Kiro-Nordic-green`.
- Cursors: bundled (`Kiro-Nordic-cursors`), referenced by the defaults — no edit needed.
- **Kvantum default ships to `/etc/xdg`, not `/etc/skel`.** `etc/xdg/Kvantum/kvantum.kvconfig`
  → `theme=Kiro-Nordic`. Kvantum's style plugin resolves its config via
  `QStandardPaths::standardLocations()` (the full XDG cascade `~/.config` → `/etc/xdg`), so
  it reads the `/etc/xdg` copy as a system fallback for every user — including the live ISO
  user — and it uninstalls cleanly as a package-owned file. `/etc/skel` only seeded *new*
  accounts and risked a shared-path clash; `/etc/xdg` mirrors how `kiro-plasma-system-settings`
  ships its defaults. Don't add an `.install` scriptlet to write it (that file would be
  unowned). Only one Kiro Plasma theme is ever installed, so no `conflicts` group is needed.
- `konsole/` excluded (Kiro ships `kiro-plasma-konsole`); `kvantum/*.tar.xz` excluded.
- Mixed delivery: payload → `/usr/share`, Kvantum selection → `/etc/xdg`. PKGBUILD copies both.
- **Must be tested on the Plasma test box** — the P6 metadata conversion needs visual
  confirmation that all three Global Themes appear and apply.
