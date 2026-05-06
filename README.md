# driftwm-noctalia

**_Noctalia Shell — native driftwm infinite canvas integration_**

<p align="center">
  <img src="https://assets.noctalia.dev/noctalia-logo.svg?v=2" alt="Noctalia Logo" style="width: 192px" />
</p>

<p align="center">
  <a href="https://github.com/youssefvdel/driftwm-noctalia">
    <img
      src="https://img.shields.io/badge/🌙_driftwm--noctalia-A8AEFF?style=for-the-badge&labelColor=0C0D11"
      alt="driftwm-noctalia"
      style="height: 50px"
    />
  </a>
</p>

<p><br/></p>

<p align="center">
  <a href="https://github.com/youssefvdel/driftwm-noctalia/commits">
    <img src="https://img.shields.io/github/last-commit/youssefvdel/driftwm-noctalia?style=for-the-badge&labelColor=0C0D11&color=A8AEFF&logo=git&logoColor=FFFFFF&label=commit" alt="Last commit" />
  </a>
  <a href="https://github.com/youssefvdel/driftwm-noctalia">
    <img src="https://img.shields.io/badge/driftwm_compatible-A8AEFF?style=for-the-badge&labelColor=0C0D11&logo=wayland&logoColor=FFFFFF" alt="driftwm compatible" />
  </a>
</p>

---

## What is this?

A fork of [Noctalia Shell](https://github.com/noctalia-dev/noctalia-shell) adapted to work natively with [driftwm](https://github.com/malbiruk/driftwm) — a trackpad-first infinite canvas Wayland compositor. Think Figma or Google Maps for your desktop.

### driftwm-native adaptations

| Area | Behavior |
|------|----------|
| **Workspaces** | Single synthetic "Canvas" workspace replaces the workspace model (driftwm has no workspaces) |
| **Wallpaper** | Auto-disabled — driftwm renders its own background via GLSL shaders |
| **Blur** | Auto-disabled — driftwm lacks `ext-background-effect-v1` protocol. The settings toggle is hidden |
| **Overview** | Auto-disabled — driftwm has no overview mode (use `Mod+W` zoom-to-fit or `Mod+A` home toggle instead) |
| **Bar** | Uses `WlrLayer.Overlay` + `keyboardFocus: OnDemand` so the bar sits above the MainScreen mask and is clickable |
| **Canvas state** | Polls `$XDG_RUNTIME_DIR/driftwm/state` every 250ms for viewport position, zoom, layout, windows, and per-output camera state |
| **Detection** | Checks `XDG_CURRENT_DESKTOP=driftwm` (display manager) with state file existence fallback (TTY mode) |
| **Window tracking** | Uses `foreign-toplevel-management` protocol — click a taskbar window and driftwm pans the viewport to center it |
| **IPC** | All standard `qs ipc` commands work (launcher, control center, notifications, session menu, bar toggle) |

---

## Feature Compatibility

| Feature | Status | Notes |
|---------|--------|-------|
| Bar / taskbar | Full | Window list, tray, clock, widgets, auto-hide |
| Launcher | Full | App search, clipboard history, session search, settings search |
| Control Center | Full | Audio, brightness, network, bluetooth, power profiles |
| Settings | Full | All panels functional. Blur toggle hidden on driftwm. Workspace-scroll hidden |
| Notifications | Full | D-Bus notifications, history, do-not-disturb |
| OSD | Full | Volume, brightness on-screen display |
| Session Menu | Full | Logout, suspend, reboot, lock |
| Lock Screen | Full | Clock, keyboard layout, session controls |
| System Tray | Full | XDG StatusNotifierItem support |
| Desktop Widgets | Full | Clock, stickers, system stats. Pinned to canvas `WlrLayer.Bottom` |
| Dock | Full | macOS-style dock with window previews |
| Plugins | Full | Plugin system inherited from upstream |
| Themes / Color Schemes | Full | Material Design 3 theming, custom schemes |
| Keyboard Layout | Partial | Shows current layout. Cycling is a no-op on driftwm |
| Display Scales | Partial | Defaults to 1.0. Callers have safe fallbacks |
| Wallpaper | Off | Auto-disabled. driftwm handles background shaders |
| Overview / Zoom-to-fit | Off | Use driftwm's built-in `Mod+W` and `Mod+A` |
| Blur Effects | Off | Protocol not supported by driftwm |

---

## Setup

### 1. Install noctalia-qs

Build the Quickshell fork from source:

```bash
git clone --depth 1 https://github.com/noctalia-dev/noctalia-qs.git ~/builds/noctalia-qs
cd ~/builds/noctalia-qs
cmake -GNinja -B build -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_INSTALL_PREFIX=/usr/local
cmake --build build
sudo cmake --install build
```

### 2. Clone this repo

```bash
mkdir -p ~/.config/quickshell
git clone https://github.com/youssefvdel/driftwm-noctalia.git ~/.config/quickshell/noctalia-shell
```

### 3. driftwm config (`~/.config/driftwm/config.toml`)

```toml
autostart = ["qs -c noctalia-shell"]

[keybindings]
"mod+d"    = "spawn qs ipc -c noctalia-shell call launcher toggle"
"mod+n"    = "spawn qs ipc -c noctalia-shell call notifications toggleHistory"
"mod+v"    = "spawn qs ipc -c noctalia-shell call launcher clipboard"
"mod+tab"  = "spawn qs ipc -c noctalia-shell call sessionMenu toggle"
```

### 4. Settings (auto-configured)

The following settings are automatically applied on driftwm:

```json
{
  "wallpaper":       { "enabled": false },
  "general":         { "enableBlurBehind": false },
  "desktopWidgets":  { "overviewEnabled": false }
}
```

No manual JSON editing needed. The shell handles this at startup.

### 5. IPC Commands

```bash
qs ipc -c noctalia-shell call launcher toggle
qs ipc -c noctalia-shell call notifications toggleHistory
qs ipc -c noctalia-shell call controlCenter toggle
qs ipc -c noctalia-shell call sessionMenu toggle
qs ipc -c noctalia-shell call bar toggle
qs ipc -c noctalia-shell show
```

---

## Known Limitations

| Limitation | Details |
|-----------|---------|
| **Keyboard layout cycling** | Widget shows the current layout but clicking it does nothing. driftwm doesn't expose layout-switch IPC. Workaround: bind a key in driftwm config |
| **Display scales** | Default to 1.0. driftwm's state file doesn't include scale info. No visual impact since all consumers use safe fallbacks |
| **Blur effects** | Not possible — driftwm doesn't implement `ext-background-effect-v1`. The blur toggle is hidden from settings UI |
| **Wallpaper management** | Not available — driftwm renders its own background via shaders. Use `~/.config/driftwm/config.toml` to configure |
| **No overview mode** | driftwm has no Niri-style overview. Use `Mod+W` (zoom-to-fit all windows) or `Mod+A` (home toggle) as alternatives |
| **Canvas state not visualized** | The shell reads canvas position/zoom from the state file but no bar widget displays it yet |

---

## What We Can Improve

### High priority

1. **Keyboard layout cycling** — Use `wlr-randr` or XKB utilities (`xkb-switch`, `xkblayout-state`) to cycle layouts as a generic fallback when the compositor doesn't provide IPC
2. **Display scale detection** — Query `wlr-randr` or the `wlr-output-management` protocol to populate display scales instead of defaulting to 1.0
3. **State poll interval** — Increase from 250ms to 500ms or use `inotify`-based file watching to reduce CPU usage (~20% at idle)

### Medium priority

4. **Canvas position widget** — Bar widget showing current zoom level and canvas coordinates. The canvas state is already polled end-to-end (DriftwmService → CompositorService → `canvasViewportX/Y/Zoom` props), just needs a UI consumer
5. **Window centering feedback** — Verify that clicking a taskbar window actually triggers driftwm's viewport pan. If not, spawn `driftwm` commands or use a workaround
6. **Background shader picker** — Integrate driftwm's shader wallpaper system into the Noctalia wallpaper panel. Could preview and switch between GLSL background shaders
7. **Home toggle keybinding** — Expose driftwm's `Mod+A` (home) and `Mod+W` (zoom-to-fit) as configurable Noctalia actions

### Low priority

8. **Window rules integration** — Settings panel for driftwm `[[window_rules]]` — position, size, decoration mode, blur (when supported), opacity, widget pinning
9. **Multi-monitor canvas awareness** — Show other monitors' viewport outlines or relative positions on the canvas
10. **State file optimization** — Parse `saved_x`/`saved_y`/`saved_zoom` (home position) if driftwm adds them to the state file
11. **Refactor backends** — Extract shared `ToplevelManager`-based window tracking from DriftwmService/LabwcService/ExtWorkspaceService into a common base
12. **Native IPC** — If/when driftwm adds a Unix socket control interface, add a proper IPC backend (similar to Hyprland's `hyprctl`)
13. **Config migration wizard** — On first run, offer to generate driftwm keybindings for common Noctalia actions
14. **Touchpad gesture passthrough** — Forward 3-finger/4-finger gestures from layer-shell surfaces to the compositor for canvas navigation

---

## Supported Compositors

| Compositor | Status | Backend |
|------------|--------|---------|
| **driftwm** | Native | `DriftwmService` (custom — state file polling + foreign-toplevel) |
| Hyprland | Inherited | `HyprlandService` (hyprctl IPC) |
| Niri | Inherited | `NiriService` (native C++ QML bindings) |
| Sway / Scroll | Inherited | `SwayService` (swaymsg IPC) |
| Labwc | Inherited | `LabwcService` (labwc-ipc + foreign-toplevel) |
| MangoWC | Inherited | `MangoService` (native IPC) |
| Generic | Inherited | `ExtWorkspaceService` (ext-workspace-v1 protocol) |

---

## Architecture

```
shell.qml (entry point)
├── Services/Compositor/CompositorService.qml  (detection router)
│   ├── DriftwmService.qml     ★ this fork's backend (317→341 lines)
│   ├── HyprlandService.qml
│   ├── NiriService.qml
│   ├── SwayService.qml
│   ├── LabwcService.qml
│   ├── MangoService.qml
│   └── ExtWorkspaceService.qml  (generic fallback)
├── Modules/                     (UI components)
│   ├── Bar/                     (taskbar + widgets)
│   ├── Panels/                  (launcher, control center, settings, etc.)
│   ├── MainScreen/              (per-output panel host + bar)
│   ├── Dock/                    (macOS-style dock)
│   ├── Background/              (wallpaper — disabled on driftwm)
│   ├── Notification/            (notification popups)
│   ├── OSD/                     (on-screen display)
│   ├── LockScreen/              (session lock)
│   └── DesktopWidgets/          (canvas widgets)
├── Services/                    (backend logic)
│   ├── Control/                 (IPC, hooks, screen detection)
│   ├── UI/                      (wallpaper, bar, panels, launcher)
│   ├── System/                  (notifications, fonts, stats)
│   ├── Media/                   (audio, media player)
│   ├── Networking/              (bluetooth, network, VPN)
│   └── Theming/                 (color schemes, templates)
└── Commons/                     (shared utilities, settings, i18n)
```

---

## Upstream

This is a fork of [Noctalia Shell](https://github.com/noctalia-dev/noctalia-shell) by [noctalia-dev](https://github.com/noctalia-dev). All credit for the original shell goes to the Noctalia team.

## License

MIT License — see [LICENSE](./LICENSE) for details.
