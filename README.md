
# 🪐 COSMIC Epoch on Ubuntu 24.04 (Regolith Wayland) via System Extensions

**Tested Configuration:**  
- **OS**: Ubuntu 24.04  
- **Session**: Regolith (Wayland)  
- **COSMIC**: Installed via [cosmic-epoch](https://github.com/pop-os/cosmic-epoch)  
- **Integration Method**: sysext (systemd system extensions)

---

## 🧩 Setup Summary

### 📦 Dependencies Installed

> Official + additional required packages for smooth build & execution:

```bash
sudo apt install just rustc mold cargo libglvnd-dev libwayland-dev libseat-dev \
libxkbcommon-dev libinput-dev udev dbus libdbus-1-dev libsystemd-dev libpixman-1-dev \
libssl-dev libflatpak-dev libpulse-dev pop-launcher libexpat1-dev libfontconfig-dev \
libfreetype-dev libgbm-dev libclang-dev libpipewire-0.3-dev libpam0g-dev -y

# Additional for applets and store to compile properly
sudo apt install libdisplay-info-dev
sudo apt install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev \
gstreamer1.0-plugins-base gstreamer1.0-plugins-good
```

> **Note**: gstreamer-sys is a Rust crate, not a package — manage via Cargo.toml.

---

## 🚀 One-Line Setup Script

```bash
git clone --recurse-submodules https://github.com/pop-os/cosmic-epoch && cd cosmic-epoch && just sysext
```

To launch:
```bash
./cosmic-launcher/target/release/cosmic-launcher
```

---

## 🎬 Proof of Work (Videos)

Video demonstrations available:

- ✅ `Untitled design.mp4`
- ✅ `Untitled design(1).mp4`
- ✅ `Untitled design(2).mp4`

  

## 🎬 Proof of Work (Image)


### 📸 Screenshot 1: Service Running with Logs

![COSMIC Settings Daemon - Running](./Screenshot%20from%202025-04-07%2020-21-51.png)

### 📸 Screenshot 2: Continuous Log Output (File Not Found)

![COSMIC Settings Daemon - Log Output](./Screenshot%20from%202025-04-07%2020-22-08.png)

---

## 🌈 Functionality Overview

| Component                 | Behavior in Regolith | Notes |
|--------------------------|----------------------|-------|
| **cosmic-settings**      | 🟡 Partially Working  | Needs cosmic-ext-sway-daemon for appearance |
| **cosmic-panel**         | 🟡 Glitchy            | Only cosmic-applet-power respects layout |
| **cosmic-launcher**      | 🔴 Unresponsive       | Likely due to missing runtime/session context |
| **cosmic-store**         | 🔴 UI only            | Install fails — write permission error |
| **cosmic-files**         | 🟢 Fully Functional    | Replaces GNOME Files without issue |
| **cosmic-editor**        | 🟢 Fully Functional    | Works as a gedit replacement |
| **cosmic-osd**           | 🔴 Crashes            | DBus and Polkit-related errors |
| **cosmic-notifications** | 🟡 Incomplete          | Works only in native COSMIC session |

---

## 🛠️ Configuration Sections Breakdown

### ✅ **What’s Configurable**

- **Display Settings** — Resolution, refresh, scale — all apply
- **Sound Settings** — Output/Input works
- **Dock & Panel Settings**  
  - Placement, opacity toggle, add/remove applets  
  - ⚠️ *Auto-hide & layout bugs in panel*

- **Appearance Settings** *(requires cosmic-ext-sway-daemon)*  
  - Accent color  
  - Gap size  
  - Active window hint sizing

### ❌ **Non-Functional Areas**

- **Input Devices**  
  - No effect on mouse/touchpad/keyboard configs

- **Power Settings**  
  - Idle timers / battery modes don’t apply

- **Firmware Information**  
  - Fails to load in About section

- **Workspaces**  
  - Workspace info missing  
  - ⬇️ Suggestion: Use swayipc (Rust) for fetching and injecting workspace data

- **Window Management Options**  
  - Minimize/maximize button toggles fail  
  - Focus mode configs don’t reflect changes  
  - Likely incompatible with Regolith’s control model → needs redesign

---

## 🧪 Applet Behavior Analysis

| Applet                   | Works in Sway? | Comments |
|--------------------------|----------------|----------|
| Power                    | ✅ Yes          | Layout aware |
| Audio, Battery, Network  | ❌ No           | Spills across workspaces |
| Input Sources            | ❌ No           | Same issue as above |
| Workspace detection      | ❌ No           | No sway integration yet |

---

## 🪲 Key Logs & Error Snapshots

### ❗ Store Installation Error

```text
unable to create '/usr/bin/7z.dpkg-new' (code 58)
```

💡 Suggestion: PackageKit attempts system-wide writes — sandbox with Flatpak or tweak permissions temporarily.

### ❗ OSD Crash

```rust
[ERROR cosmic_settings_subscriptions::upower::kbdbacklight] Error: Object does not exist at path “/org/freedesktop/UPower/KbdBacklight”

called `Result::unwrap()` on an `Err` value:
org.freedesktop.PolicyKit1.Error.Failed: An authentication agent already exists
```

🧠 Root Cause: Conflicting authentication agents between Regolith & COSMIC.

---

## 🧬 Suggestions for Integration Fixes

- 🌐 **Workspaces**: Add sway workspace detection via swayipc crate
- 🖱️ **Input Settings**: Stub or port Regolith’s device management to Cosmic’s UI
- 🪟 **Window Management**: Rebuild UI hooks specific to Regolith’s tiling behavior
- 🔒 **Authentication Agents**: Avoid .unwrap() where multiple polkit agents may exist
- ⚡ **Launcher Debugging**: Start with verbose logs or under native COSMIC session
