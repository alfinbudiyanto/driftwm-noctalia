# driftwm-desktop Roadmap

**_Unified desktop: driftwm compositor + Noctalia shell_**

---

## Open driftwm Issues (upstream)

| # | Title | Type | Priority for us |
|---|-------|------|-----------------|
| [#70](https://github.com/malbiruk/driftwm/issues/70) | **IPC** — add Unix socket control interface | enhancement | **Critical** — would let us switch layouts, query state, control canvas |
| [#66](https://github.com/malbiruk/driftwm/issues/66) | **SSD text header** — show window titles in server-side decorations | enhancement | Low |
| [#61](https://github.com/malbiruk/driftwm/issues/61) | **NVIDIA** — cursor jitter, fullscreen stutter, input lag | bug | Low (user has AMD/NVIDIA?) |
| [#46](https://github.com/malbiruk/driftwm/issues/46) | **XWayland** — xwayland-satellite issues | bug | Medium (affects Steam, JetBrains, Wine) |
| [#37](https://github.com/malbiruk/driftwm/issues/37) | **Canvas ideas** — sticky notes, minimap, canvas bookmarks | enhancement | High (aligns with our canvas widget roadmap) |
| [#34](https://github.com/malbiruk/driftwm/issues/34) | **Animations** — open/close/move window animations | enhancement | Low |
| [#22](https://github.com/malbiruk/driftwm/issues/22) | **Bezel correction** — multi-monitor gap compensation | enhancement | Medium (3-monitor setup) |
| [#4](https://github.com/malbiruk/driftwm/issues/4) | **Touchscreen** — touch input support | enhancement | Low |

---

## ✅ Completed

### Compositor integration
- [x] Auto-detection (`XDG_CURRENT_DESKTOP` + state file fallback)
- [x] Window tracking via `foreign-toplevel-management`
- [x] Session management (logout, monitor off/on)
- [x] Display scales via `wlr-randr --json`
- [x] Keyboard layout display from state file
- [x] Canvas state polling (`$XDG_RUNTIME_DIR/driftwm/state`)
- [x] Auto-disable wallp

aper/blur/overview

### Shell integration
- [x] Bar with driftwm layer fix (`WlrLayer.Overlay` + `OnDemand`)
- [x] All IPC commands (launcher, control center, notifications, session menu)
- [x] Settings UI adapted (hide workspace/blur options)
- [x] Canvas zoom widget
- [x] Telemetry reporting "DriftWM"

### Infrastructure
- [x] PR #2630 to upstream noctalia
- [x] CPU optimization (500ms poll interval)
- [x] Installation guide with per-distro instructions
- [x] Unified project structure (`driftwm-desktop/`)

---

## 🔜 Next Up (short-term)

| # | Feature | Effort | Files |
|---|---------|--------|-------|
| 1 | **State file → inotify** — replace 500ms polling with `FileWatcher` (zero overhead) | S | `DriftwmService.qml` |
| 2 | **Home/Zoom-to-fit keybinds** — expose `Mod+A`/`Mod+W` as noctalia actions | S | `DriftwmService.qml` + keybind |
| 3 | **Window rules UI** — settings panel for driftwm `[[window_rules]]` | M | New settings tab |
| 4 | **Canvas bookmarks** — save/restore viewport positions (aligns with #37) | M | New panel + state file |
| 5 | **Background shader picker** — browse/switch GLSL wallpapers from settings | M | `WallpaperPanel.qml` + state file |

---

## 🗺️ Medium-term

| # | Feature | Depends on | Effort |
|---|---------|-----------|--------|
| 6 | **Keyboard layout cycling** — actual switch via compositor (blocked on driftwm IPC #70) | IPC | M |
| 7 | **Multi-monitor canvas indicators** — show other viewports on the canvas | — | M |
| 8 | **Workspace bookmarks** — named canvas positions with quick-jump | Canvas bookmarks | M |
| 9 | **Touchpad gesture passthrough** — forward 3/4-finger from layer surfaces | driftwm gesture API | L |
| 10 | **Config migration wizard** — generate driftwm keybinds on first noctalia run | — | M |

---

## 🏗️ Long-term / Blocked

| # | Feature | Blocked by | Effort |
|---|---------|-----------|--------|
| 11 | **Native IPC backend** — proper socket-based communication | driftwm IPC (#70) | L |
| 12 | **Layout switching** — cycle keyboard layouts via compositor | driftwm IPC (#70) | M |
| 13 | **Canvas minimap** — bird's-eye view of all windows (aligns with #37) | driftwm API | L |
| 14 | **Compositor health monitoring** — CPU/GPU stats, frame timing | driftwm IPC | M |
| 15 | **Gesture configuration UI** — GUI for driftwm gesture bindings | driftwm config schema | L |
| 16 | **Shader IDE** — in-shell GLSL editor with live preview | — | L |

---

## 🔗 Related Issues

| driftwm Issue | Our Roadmap Item | Relationship |
|---------------|-----------------|--------------|
| [#70](https://github.com/malbiruk/driftwm/issues/70) IPC | #11 Native IPC backend | Blocks proper integration |
| [#70](https://github.com/malbiruk/driftwm/issues/70) IPC | #6 Keyboard layout cycling | Would enable real switching |
| [#37](https://github.com/malbiruk/driftwm/issues/37) Canvas ideas | #4 Canvas bookmarks | Aligned goals |
| [#37](https://github.com/malbiruk/driftwm/issues/37) Canvas ideas | #13 Canvas minimap | Aligned goals |
| [#46](https://github.com/malbiruk/driftwm/issues/46) XWayland | — | Affects user experience |
| [#34](https://github.com/malbiruk/driftwm/issues/34) Animations | — | Nice-to-have polish |

---

## 📁 Project Structure

```
driftwm-desktop/
├── compositor/          — driftwm source (malbiruk/driftwm)
│   ├── src/
│   │   ├── state/persistence.rs  (state file format)
│   │   └── ...
│   └── docs/
├── shell/               — noctalia shell (youssefvdel/driftwm-noctalia)
│   ├── Services/Compositor/
│   │   └── DriftwmService.qml    (our backend)
│   ├── Modules/
│   └── ...
├── graphify-out/        — unified knowledge graph
└── ROADMAP.md           — this file
```
