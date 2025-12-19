# 🚀 Arch Linux Minimal & Optimized Laptop Setup (LXQt/LabWC)

This document outlines the configuration, installations, and maintenance commands for setting up a power-efficient and lightweight Arch Linux system using the **LXQt** desktop environment on the **LabWC** Wayland compositor.

---

## 🔒 BIOS / Firmware Configuration (Hardening & Power)

Apply these settings in your system's BIOS/UEFI to improve security, reduce attack surface, and enhance power efficiency by disabling unnecessary hardware interfaces.

| BIOS Section | Setting | Recommended Value |
| :--- | :--- | :--- |
| **Config -> Thunderbolt (TM) 3** | Thunderbolt BIOS Assist Mode | **Disabled** |
| **Config -> Network** | Wake On LAN | **Disabled** |
| **Config -> Network** | Wake On LAN from Dock | **Disabled** |
| **Security -> I/O Port Access** | Ethernet LAN | **Disabled** |
| **Security -> I/O Port Access** | Wireless WAN | **Disabled** |
| **Security -> I/O Port Access** | Memory Card Slot | **Disabled** |
| **Security -> I/O Port Access** | Smart Card Slot | **Disabled** |
| **Security -> I/O Port Access** | Thunderbolt (TM) 3 | **Disabled** |

---

## 🛠️ Installation & Setup

### 1. Install `yay` (AUR Helper)

`yay` is the recommended AUR helper for easily installing, updating, and managing packages from the Arch User Repository (AUR).

```
sudo pacman -S --needed git base-devel && git clone [https://aur.archlinux.org/yay.git](https://aur.archlinux.org/yay.git) && cd yay && makepkg -si
```

After installation, initialize the database:

```
yay -Y --gendb
```

### 2. Install Core Software

Use `yay` to install the essential packages, grouped by their function:

#### 💻 Wayland / LXQt Core

| Name | Purpose | Installation Command |
| :--- | :--- | :--- |
| **labwc** | Minimalist window-stacking **Wayland compositor** (core of the desktop). | `yay -S labwc` |
| **lxqt-wayland-session** | Provides necessary files to launch the LXQt session under Wayland. | `yay -S lxqt-wayland-session` |
| **qt5-declarative** | Development files for **Qt 5's QML/declarative framework** (often a dependency for Qt apps/widgets). | `yay -S qt5-declarative` |
| **kanshi** | Dynamic output configuration manager for Wayland (automatically switches display profiles). | `yay -S kanshi` |
| **wdisplays** | Graphical utility for configuring display outputs (resolution, position, rotation) on Wayland. | `yay -S wdisplays` |
| **labwc-tweaks** | Qt-based GUI configuration tool for managing `labwc` settings and themes. | `yay -S labwc-tweaks-git` |

#### ⚙️ System Utilities & Services

| Name | Purpose | Installation Command |
| :--- | :--- | :--- |
| **bluez & blueman** | The official Linux Bluetooth stack and the GTK+ Bluetooth Manager (GUI). | `yay -S bluez blueman` |
| **nm-tray** | Simple **Qt-based NetworkManager frontend** that resides in the system tray. | `yay -S nm-tray` |
| **gvfs** | GNOME Virtual File System for accessing remote filesystems (SFTP, SMB). | `yay -S gvfs` |
| **fwupd** | Daemon to manage the installation of **firmware updates** (UEFI/Capsule). | `yay -S fwupd` |
| **stress-ng** | Tool to stress test a computer system for stability and debugging. | `yay -S stress-ng` |

#### ⚡ Power & Hardware Management

| Name | Purpose | Installation Command |
| :--- | :--- | :--- |
| **TLP & TLPUI** | Feature-rich power management tool and its GTK graphical user interface. | `yay -S tlp tlp-rdw tlpui` |
| **intel-ucode** | Critical **Intel processor microcode updates** for security and bug fixes. | `yay -S intel-ucode` |
| **intel-undervolt** | Utility for **undervolting Intel CPUs** to reduce heat and improve power efficiency. | `yay -S intel-undervolt` |

#### 📝 Desktop Applications

| Name | Purpose | Installation Command |
| :--- | :--- | :--- |
| **featherpad** | Lightweight, independent Qt text editor. | `yay -S featherpad` |
| **qalculate-qt** | Versatile, cross-platform desktop calculator (Qt version). | `yay -S qalculate-qt` |

> [!IMPORTANT]
> Remember to check documentation for commands required to **enable and start** services like `tlp` and `bluez`.

### 3. Install Fonts

Install the **Inter** font family and the **Nerd Fonts Symbols** package for comprehensive glyph and icon support.

```
yay -S inter-font ttf-nerd-fonts-symbols
```
---

## ⚡ System Optimization & Power Efficiency

### 1. Wayland Environment Variables

Set these environment variables in your LXQt Session Settings (or `~/.profile`) for maximum performance and battery savings on the Wayland compositor. 

| Variable | Value | Purpose |
| :--- | :--- | :--- |
| `MOZ_ENABLE_WAYLAND` | `1` | Forces Firefox to use the native Wayland backend for maximum speed and efficiency. |
| `MOZ_VA_API_ALL_DRIVERS` | `1` | Forces Firefox VA-API hardware video decoding, drastically reducing **CPU load and saving battery** during video playback. |
| `MOZ_ENABLE_DMABUF` | `1` | Enables Firefox Direct Memory Access Buffer sharing, reducing memory copies for better rendering efficiency. |
| `MOZ_ACCELERATE_VSYNC` | `1` | Forces Firefox VSync to be handled by the Wayland compositor for smoother, more efficient rendering. |
| `QT_QPA_PLATFORM` | `wayland` | Forces Qt applications (like LXQt components) to use the native Wayland protocol for efficiency. |
| `GDK_BACKEND` | `wayland` | Tells GTK applications to prefer the native Wayland backend over XWayland. |

### 2. Taming Unnecessary Services

Stop and mask services that are often not required on a minimal laptop setup, saving boot time and resources.

* **Stop and Mask Accessibility Services (AT-SPI):**
    This often reduces memory use and latency if you do not use screen readers or other accessibility tools.
    ```bash
    systemctl --user mask at-spi-dbus-bus.service
    systemctl --user stop at-spi-dbus-bus.service
    systemctl --user mask org.a11y.atspi.Registry.service
    systemctl --user stop org.a11y.atspi.Registry.service
    ```

### 3. Cleaning Up Packages

Uninstall redundant or less efficient packages:
```
sudo pacman -Rns network-manager-applet l3afpad vim
```

---

## 💻 System Maintenance

### 1. Full System Update & Cleanup

This comprehensive command updates the system, removes orphaned packages and their unused dependencies, and cleans the package cache, keeping only installed packages.

```
yay -Syyu --noconfirm && yay -Yc --noconfirm && yay -Sc --noconfirm
```

Alternatively, using pacman for orphaned packages:
```
sudo pacman -Syyu && sudo pacman -Rns $(pacman -Qdtq) && sudo pacman -Sc
```

### 2. Enable SSD TRIM

For optimal SSD longevity and performance, enable the weekly TRIM service:

```
sudo systemctl enable --now fstrim.timer
```

Check status:

```
sudo systemctl status fstrim.timer
```

### 3. Taming the Journal Size

Limit the size and age of the systemd journal logs to prevent disk space consumption:

```
sudo journalctl --vacuum-size=100M
sudo journalctl --vacuum-time=2weeks
```

---

## 🔍 System Analysis & Debugging

### 1. Analyzing the Boot Process

Check and debug slow boot times:

```
sudo systemd-analyze
sudo systemd-analyze blame
sudo systemd-analyze critical-chain
```

### 2. Check for System Errors

Identify any failed services and check the journal for critical errors:

```
sudo systemctl --failed
sudo journalctl -p 3 -xb
```

### 3. Running Services and Timers

List active services and timers for debugging or optimization purposes:

```
systemctl list-units --type=service --state=running
systemctl list-timers
systemctl --user list-units --type=service --state=running
```

---

## 🖥️ User Experience & Boot

### 1. Keeping the Boot Output Minimal

Suppress most kernel messages during boot for a cleaner startup screen and enable **Early Kernel Mode Setting (KMS)** for smoother graphics initialization (best practice for Intel graphics). 

Edit `/etc/kernel/cmdline` and add the following kernel parameters to the end of your `.conf` file:

```
quiet splash loglevel=3 i915.modeset=1
```

### 2. Custom Command Widget - Battery Stats

This command provides detailed battery statistics (percentage, remaining time, and power draw in Watts) for use in status bars or custom widgets.

```
sh -c 'BAT_SYS_PATH="/sys/class/power_supply/BAT0"; BAT_UPOWER_PATH="/org/freedesktop/UPower/devices/battery_BAT0"; watt=$(awk "{printf \" %.2f W\", \$1 / 1000000}" "$BAT_SYS_PATH/power_now"); perc=$(cat "$BAT_SYS_PATH/capacity")%; time=$(upower -i "$BAT_UPOWER_PATH" | awk "/time/ {print \" \"\$4, \$5}"); echo "|  $perc | $time | $watt |"'
```

## 🎨 Theming

### 1. Catppuccin Frappé

This table provides the specific hex codes to achieve a Frappé look within the LXQt Appearance settings.

| UI Element | Catppuccin Role | Hex Code | Swatch |
| :--- | :--- | :--- | :--- |
| **Window** | Base | `#303446` | ![#303446](https://via.placeholder.com/15/303446/000000?text=+) |
| **Window Text** | Text | `#c6d0f5` | ![#c6d0f5](https://via.placeholder.com/15/c6d0f5/000000?text=+) |
| **View (List/Input)** | Mantle | `#292c3c` | ![#292c3c](https://via.placeholder.com/15/292c3c/000000?text=+) |
| **View Text** | Text | `#c6d0f5` | ![#c6d0f5](https://via.placeholder.com/15/c6d0f5/000000?text=+) |
| **Selection** | Lavender | `#babbf1` | ![#babbf1](https://via.placeholder.com/15/babbf1/000000?text=+) |
| **Selected Text** | Base | `#303446` | ![#303446](https://via.placeholder.com/15/303446/000000?text=+) |
| **Tooltip** | Mantle | `#292c3c` | ![#292c3c](https://via.placeholder.com/15/292c3c/000000?text=+) |
| **Tooltip Text** | Text | `#c6d0f5` | ![#c6d0f5](https://via.placeholder.com/15/c6d0f5/000000?text=+) |
| **Link** | Blue | `#8caaee` | ![#8caaee](https://via.placeholder.com/15/8caaee/000000?text=+) |
| **Visited Link** | Mauve | `#ca9ee6` | ![#ca9ee6](https://via.placeholder.com/15/ca9ee6/000000?text=+) |
| **Taskbar BG** | Crust | `#232634` | ![#232634](https://via.placeholder.com/15/232634/000000?text=+) |
```

LXQT themes command

sudo cp -r /home/rui/Downloads/catppuccin-frappe /usr/share/lxqt/themes/



### 2. Catppuccin Latte

This table provides the specific hex codes for the Latte (Light) flavor, mapped to the LXQt Appearance elements.

| UI Element | Catppuccin Role | Hex Code | Swatch |
| :--- | :--- | :--- | :--- |
| **Window** | Base | `#eff1f5` | ![#eff1f5](https://via.placeholder.com/15/eff1f5/000000?text=+) |
| **Window Text** | Text | `#4c4f69` | ![#4c4f69](https://via.placeholder.com/15/4c4f69/000000?text=+) |
| **View (List/Input)** | Mantle | `#e6e9ef` | ![#e6e9ef](https://via.placeholder.com/15/e6e9ef/000000?text=+) |
| **View Text** | Text | `#4c4f69` | ![#4c4f69](https://via.placeholder.com/15/4c4f69/000000?text=+) |
| **Selection** | Lavender | `#7287fd` | ![#7287fd](https://via.placeholder.com/15/7287fd/000000?text=+) |
| **Selected Text** | Base | `#eff1f5` | ![#eff1f5](https://via.placeholder.com/15/eff1f5/000000?text=+) |
| **Tooltip** | Mantle | `#e6e9ef` | ![#e6e9ef](https://via.placeholder.com/15/e6e9ef/000000?text=+) |
| **Tooltip Text** | Text | `#4c4f69` | ![#4c4f69](https://via.placeholder.com/15/4c4f69/000000?text=+) |
| **Link** | Blue | `#1e66f5` | ![#1e66f5](https://via.placeholder.com/15/1e66f5/000000?text=+) |
| **Visited Link** | Mauve | `#8839ef` | ![#8839ef](https://via.placeholder.com/15/8839ef/000000?text=+) |
| **Taskbar BG** | Crust | `#dce0e8` | ![#dce0e8](https://via.placeholder.com/15/dce0e8/000000?text=+) |
