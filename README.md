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
  | **labwc-tweaks** | Qt-based GUI configuration tool for managing `labwc` settings and themes. | `yay -S labwc-tweaks-git` |
  | **lxqt-wayland-session** | Provides necessary files to launch the LXQt session under Wayland. | `yay -S lxqt-wayland-session` |
  | **qt5-declarative** | Development files for **Qt 5's QML/declarative framework**. | `yay -S qt5-declarative` |
  
  #### 🖥️ Display & Output Control
  
  | Name | Purpose | Installation Command |
  | :--- | :--- | :--- |
  | **brightnessctl** | Lightweight tool to read and control **device backlight** (monitor brightness). | `yay -S brightnessctl` |
  | **kanshi** | Dynamic output configuration manager for Wayland (automatically switches display profiles). | `yay -S kanshi` |
  | **swayidle** | Idle management daemon (used for locking the screen or turning off monitors). | `yay -S swayidle` |
  | **waylock** | Simple **screen locker** for Wayland compositors. | `yay -S waylock` |
  | **wdisplays** | Graphical utility for configuring display outputs (resolution, position, rotation). | `yay -S wdisplays` |
  | **wlopm** | Wayland output power management (allows turning monitors on/off via CLI). | `yay -S wlopm` |
  | **wlsunset** | Day/night gamma mask for Wayland (Blue light filter). | `yay -S wlsunset` |
  
  #### ⚙️ System Utilities & Services
  
  | Name | Purpose | Installation Command |
  | :--- | :--- | :--- |
  | **bluez & blueman** | The official Linux Bluetooth stack and the GTK+ Bluetooth Manager (GUI). | `yay -S bluez blueman` |
  | **fwupd** | Daemon to manage the installation of **firmware updates** (UEFI/Capsule). | `yay -S fwupd` |
  | **gvfs** | GNOME Virtual File System for accessing remote filesystems (SFTP, SMB). | `yay -S gvfs` |
  | **nm-tray** | Simple **Qt-based NetworkManager frontend** that resides in the system tray. | `yay -S nm-tray` |
  | **stress-ng** | Tool to stress test a computer system for stability and debugging. | `yay -S stress-ng` |
  | **unzip** | Extraction utility for compressed **.zip** archives. | `yay -S unzip` |
  
  #### ⚡ Power & Hardware Management
  
  | Name | Purpose | Installation Command |
  | :--- | :--- | :--- |
  | **intel-ucode** | Critical **Intel processor microcode updates** for security and bug fixes. | `yay -S intel-ucode` |
  | **intel-undervolt** | Utility for **undervolting Intel CPUs** to reduce heat and improve power efficiency. | `yay -S intel-undervolt` |
  | **TLP & TLPUI** | Feature-rich power management tool and its GTK graphical user interface. | `yay -S tlp tlp-rdw tlpui` |
  
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
  
  ### 2. Kernel: Early Modesetting (KMS)
  To prevent screen flickering and ensure the Intel UHD 620 graphics are initialized as early as possible, the `i915` module is loaded during the initramfs stage.
  
  1. Edit `/etc/mkinitcpio.conf`:
  
  ```
  MODULES=(i915)
  ```
  
  Regenerate the initramfs:
  ```
  sudo mkinitcpio -P
  ```
  
  ### 3. Systemd: TPM & Security Masking
  
  If you suse a standard BTFRS partition without LUKS encryption, the 5+ second delay caused by the system polling the TPM 2.0 can be eliminated by masking the relevant services.
  
  ```
  sudo systemctl mask \
    systemd-tpm2-setup-early.service \
    systemd-tpm2-setup.service \
    systemd-pcrproduct.service \
    systemd-pcrphase.service \
    systemd-pcrphase-sysinit.service \
    systemd-pcrmachine.service
  ```
  
  ### 4. Network: Non-Blocking Startup
  
  By default, some systems wait for a confirmed internet connection before reaching the login manager. Disabling this service allows the desktop to load while Wi-Fi connects in the background.
  
  ```
  sudo systemctl mask NetworkManager-wait-online.service
  ```
  
  ### 5. User Interface: Accessibility (AT-SPI) Cleanup
  
  To reduce memory overhead and slight app-launch latency, accessibility bus services can be disabled.
  
  ```
  systemctl --user mask at-spi-dbus-bus.service
  systemctl --user mask org.a11y.atspi.Registry.service
  systemctl --user stop at-spi-dbus-bus.service
  systemctl --user stop org.a11y.atspi.Registry.service
  ```

  ### 6. Bluetooth "Auto-On" Disable

  By default, Bluetooth often powers on at every boot, wasting battery if you don't use it.

  Edit /etc/bluetooth/main.conf and set: 

  ```
  AutoEnable=false
  ```
  > [!IMPORTANT]
  > If it has a # at the beginning of the line, delete the # to "uncomment" it.
  
  ### 7. Cleaning Up Packages
  
  Uninstall redundant or less efficient packages:
  ```
  sudo pacman -Rns network-manager-applet l3afpad vim
  ```
  
  ---
  
  ## 💻 System Maintenance
  
  ### 1. Full System Update & Cleanup
  
  This comprehensive command updates the system, removes orphaned packages and their unused dependencies, and cleans the package cache, keeping only installed packages.
  
  ```
  sudo rm -rf /var/cache/pacman/pkg/download-* && yay -Syu --noconfirm && yay -Yc --noconfirm && yay -Sc --noconfirm
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

  Execute the command:

  ```
  sudo featherpad /etc/systemd/journald.conf
  ```

  Find and change these lines (remove the # at the start):

  ```
  SystemMaxUse=100M
  MaxRetentionSec=2week
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

  systemctl list-units --type=service --state=active
  systemctl --failed
  systemctl --user list-units --type=service --state=active
  systemctl list-timers 
  ```
  
  ---
  
  ## 🖥️ User Experience & Boot
  
  ### 1. Optimized Kernel Boot Parameters
  
  These flags focus on silencing the console and skipping unnecessary hardware timeouts.
  
  | Parameter | Function | Impact |
  | :--- | :--- | :--- |
  | `quiet` | Suppresses kernel logs | Cleaner, flicker-free boot experience. |
  | `splash` | Enables Boot Splash | Prepares the system to show a splash image/logo. |
  | `loglevel=3` | Error Filtering | Only shows critical system errors; hides non-essential warnings. |
  | `rd.systemd.show_status=auto` | Smart Status | Hides "Started [Service Name]" messages unless a service fails. |
  | `rd.udev.log_priority=3` | Udev Silence | Prevents hardware discovery logs from cluttering the screen. |
  | `tpm_tis.interrupts=0` | TPM Optimization | Fixes a common ThinkPad bug where the TPM chip hangs the boot. |
  | `8250.nr_uarts=0` | Disable Serial Probing | Skips the search for legacy 9-pin serial ports. |
  | `i915.modeset=1` | Early KMS | Forces Intel graphics to load early to match native screen resolution. |
  
  Edit `/etc/kernel/cmdline` and add the following kernel parameters to the end of the `.conf` file:
  
  ```
  quiet splash loglevel=3 rd.systemd.show_status=auto rd.udev.log_priority=3 tpm_tis.interrupts=0 8250.nr_uarts=0 i915.modeset=1
  ```
  
  Run the following command to rebuild the image and "bake" these new flags into the UKI.
  
  ```
  sudo mkinitcpio -P
  ```
  
  ### 2. Initramfs Configuration (`mkinitcpio.conf`)
  
  The `initramfs` is a small environment that loads the hardware drivers needed to mount the root file system. This is an optimized hooks, in order to prioritize stability and boot speed.
  
  | Hook | Function | Impact |
  | :--- | :--- | :--- |
  | **`microcode`** | CPU Patching | **Critical:** Must be first. Patches Intel CPU bugs before the image is unpacked. |
  | **`systemd`** | Init Controller | Replaces the legacy `base` hook for a faster, parallelized boot process. |
  | **`autodetect`** | Image Shrinker | Scans the T480s hardware to keep the boot image as small as possible. |
  | **`modconf`** | Module Config | Includes custom driver configurations (like undervolting or GPU tweaks). |
  | **`kms`** | Graphics Setup | Enables Early Kernel Mode Setting for a flicker-free transition to the GUI. |
  | **`keyboard`** | Input Support | Ensures the laptop keyboard works during the very early stages of boot. |
  | **`sd-vconsole`** | Virtual Console | Sets the correct system font and keyboard layout for the terminal. |
  | **`block`** | Storage Drivers | Provides the necessary drivers to communicate with the NVMe SSD. |
  | **`filesystems`** | FS Support | Allows the kernel to mount and read the **Btrfs** root partition. |

> [!IMPORTANT]
> The fsck hook has been removed. Since this setup uses Btrfs, the standard fsck is unnecessary; removing it prevents cosmetic "exit status 8" errors in the boot logs.
  
  Edit `/etc/mkinitcpio.conf` and edit the hooks line, to look like this:
  
  ```
  HOOKS=(microcode systemd autodetect modconf kms keyboard sd-vconsole block filesystems)
  ```
  
  Run the following command to "bake" "bake" these hooks into the .efi binary that the BIOS actually executes.
  
  ```
  sudo mkinitcpio -P
  ```
  
  ### 3. Custom Command Widget - Battery Stats
  
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
  
  ### 3. LXQT themes command
  
  ```
  sudo cp -r /home/rui/Downloads/catppuccin-frappe /usr/share/lxqt/themes/
  ```
