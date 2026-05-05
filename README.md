# driftwm-noctalia

**_Noctalia Shell — adapted for driftwm infinite canvas_**

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

A fork of [Noctalia Shell](https://github.com/noctalia-dev/noctalia-shell) adapted to work natively with [driftwm](https://github.com/malbiruk/driftwm) — a trackpad-first infinite canvas Wayland compositor.

**Key differences from upstream Noctalia:**

- **No workspaces** — driftwm uses an infinite 2D canvas instead of workspaces. A single synthetic "Canvas" workspace replaces the workspace model.
- **Bar clickability fix** — bar uses `WlrLayer.Overlay` + `keyboardFocus: OnDemand` on driftwm so it sits above the MainScreen mask.
- **Blur auto-disabled** — driftwm doesn't support `ext-background-effect-v1`, so blur is automatically disabled.
- **Canvas state polling** — reads `$XDG_RUNTIME_DIR/driftwm/state` for viewport position, zoom, and window/layer lists.
- **Auto-detection** — detects driftwm via `XDG_CURRENT_DESKTOP=driftwm` at startup.

## Supported Compositors

| Compositor | Status |
|---|---|
| **driftwm** | Native (this fork) |
| Hyprland | Inherited from upstream |
| Niri | Inherited from upstream |
| Sway / Scroll | Inherited from upstream |
| Labwc | Inherited from upstream |
| MangoWC | Inherited from upstream |

## driftwm Setup

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
"mod+d" = "spawn qs ipc -c noctalia-shell call launcher toggle"
"mod+n" = "spawn qs ipc -c noctalia-shell call notifications toggleHistory"
"mod+v" = "spawn qs ipc -c noctalia-shell call launcher clipboard"
```

### 4. Noctalia settings (`~/.config/noctalia/settings.json`)

```json
{
  "wallpaper": { "enabled": false },
  "general": { "enableBlurBehind": false },
  "desktopWidgets": { "overviewEnabled": false },
  "notifications": { "overlayLayer": false },
  "osd": { "overlayLayer": false }
}
```

Wallpaper must be disabled — driftwm handles its own background via shaders.

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

## Upstream

This is a fork of [Noctalia Shell](https://github.com/noctalia-dev/noctalia-shell) by [noctalia-dev](https://github.com/noctalia-dev). All credit for the original shell goes to the Noctalia team.

## License

MIT License — see [LICENSE](./LICENSE) for details.
