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
  sudo pacman -S --needed git base-devel && git clone https://aur.archlinux.org/yay.git && cd yay && makepkg -si
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
## 🧠 Memory Management: ZRAM Optimization

Since this setup utilizes Btrfs on an NVMe SSD, ZRAM is used instead of a traditional swap partition/file. This creates a compressed swap device in RAM, which is significantly faster than swapping to disk and extends the lifespan of the SSD.

### 1. Disable ZSwap

ZSwap is a kernel feature that acts as a cache for a disk-based swap. Since we are using ZRAM, ZSwap is redundant and can be disabled via the kernel parameter included in your cmdline (check `/etc/kernel/cmdline`):

```
zswap.enabled=0
```

## 2. Install and Configure zram-generator

I use zram-generator because it is written in Rust, extremely lightweight, and integrates directly with systemd.

```
yay -S zram-generator
```

To configure it, create / edit `/etc/systemd/zram-generator.conf`, and add the following:

```
[zram0]
zram-size = ram / 2
compression-algorithm = zstd
swap-priority = 100
fs-type = swap
```

For systems with 8 GB RAM, consider ram / 3 instead of ram / 2.

### 3. Tuning Swappiness

To ensure the system uses the compressed ZRAM effectively before touching the disk, we adjust the kernel's swappiness.

To do that, create / edit `/etc/sysctl.d/99-vm-zram-parameters.conf`,  and add the following:

```
vm.swappiness = 180
vm.watermark_boost_factor = 0
vm.watermark_scale_factor = 125
vm.page-cluster = 0
```

### 4. Verification

After a reboot, verify that your ZRAM is active and using the zstd algorithm:

```
zramctl
```

You should see /dev/zram0 listed with a high compression ratio.

  ---
  
  ## ⚡ System Optimization & Power Efficiency
  
  ### 1. Wayland Environment Variables
  
  Set these environment variables in your LXQt Session Settings for maximum performance and battery savings on the Wayland compositor. 
  
  | Variable | Value | Purpose |
  | :--- | :--- | :--- |
  | `MOZ_ENABLE_WAYLAND` | `1` | Forces Firefox to use the native Wayland backend for maximum speed and efficiency. |
  | `MOZ_VA_API_ALL_DRIVERS` | `1` | Forces Firefox VA-API hardware video decoding, drastically reducing **CPU load and saving battery** during video playback. |
  | `MOZ_ENABLE_DMABUF` | `1` | Enables Firefox Direct Memory Access Buffer sharing, reducing memory copies for better rendering efficiency. |
  | `MOZ_ACCELERATE_VSYNC` | `1` | Forces Firefox VSync to be handled by the Wayland compositor for smoother, more efficient rendering. |
  | `QT_QPA_PLATFORM` | `wayland` | Forces Qt applications (like LXQt components) to use the native Wayland protocol for efficiency. |
  | `GDK_BACKEND` | `wayland` | Tells GTK applications to prefer the native Wayland backend over XWayland. |
  | `GTK_MODULES` | `""` | Prevents GTK from searching for legacy accessibility modules. |
  | `NO_AT_BRIDGE` | `1` | Disables the AT-SPI accessibility bus to reduce overhead and launch lag. |

### 2. Wireless Networking with iwd (iNet Wireless Daemon)

I have replaced the default `wpa_supplicant` with `iwd`. It is written by Intel specifically for Linux, offering faster connection times, lower memory footprint, and a much cleaner command-line interface.
`iwd`also handles the "roaming" between **2.4GHz and 5GHz** bands much more gracefully than the old `wpa_supplicant`, which helps prevent those annoying 1-second drops when moving the laptop around the house; it also significantly improves the **"Resume from Sleep"** time.

To use `iwd` as the backend for **NetworkManager**, create this configuration file:

```
sudo nano /etc/NetworkManager/conf.d/wifi_backend.conf
```

and add the following lines:

```
[device]
wifi.backend=iwd
```

After saving, disable the old wpa_supplicant service to prevent conflicts:

```
sudo systemctl disable --now wpa_supplicant.service
```

And enable the iwd service:

```
sudo systemctl enable --now iwd.service
```

#### iwd Cheat Sheet (iwctl)

Quick scan and list:

```
iwctl station wlan0 scan && iwctl station wlan0 get-networks
```

Connect to your home Wi-Fi instantly:

```
iwctl --passphrase "YOUR_PASSWORD" station wlan0 connect "YOUR_SSID"
```

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
  
If you use a standard Btfrs partition without LUKS encryption, the 5+ second delay caused by the system polling the TPM 2.0 can be eliminated by masking the relevant services.
This way, we are avoiding a Software Delay, by preventing systemd from waiting for the chip to initialize
  
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
  sudo pacman -Sc --noconfirm && yay -Syu --noconfirm && yay -Yc --noconfirm && yay -Sc --noconfirm
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
  sudo nano /etc/systemd/journald.conf
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
  echo "--- KERNEL ERRORS ---"; sudo journalctl -p 3 -xb --no-hostname --no-pager; echo "--- POWER & VOLTAGE ---"; sudo journalctl -u tlp -u intel-undervolt -b; echo "--- BTRFS HEALTH ---"; sudo journalctl -t btrfs -b; echo "--- ZRAM STATUS ---"; zramctl
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
  
The Linux kernel’s behavior at startup is governed by boot-time parameters. By default, Linux is very talkative and cautious, so for this build, the command line has been tuned with three primary objectives:

**Look Better:** Stops the "text wall" and flashing during boot so you get a clean, smooth transition to your desktop.

**Boot Faster:** Tells the computer to stop searching for old hardware that the T480s doesn't even have (like old serial ports).

**Save Battery:** Forces your hardware (SSD, Wi-Fi, and GPU) to take "micro-naps" whenever you aren't moving the mouse, which adds up to significantly more battery life.
  
| Parameter | Function | Benefit |
| :--- | :--- | :--- |
| **`quiet`** | Minimal Boot Verbosity | Removes the "wall of text" during startup for a cleaner look. |
| `splash` | Boot Splash Support | Allows the use of a graphical boot logo (like Plymouth/BGRT). |
| `loglevel=3` | Error Suppression | Filters out non-critical kernel "noise" while keeping errors visible. |
| `rd.systemd.show_status=auto` | Smart Service Status | Only shows systemd service logs if a service fails to start. |
| `rd.udev.log_priority=3` | Udev Verbosity Control | Hides hardware initialization logs for a seamless transition to the desktop. |
| `tpm_tis.interrupts=0` | TPM Polling Fix | Prevents the system from hanging while waiting for TPM interrupts. |
| `modprobe.blacklist=tpm_tis,tpm_tis_core` | TPM Hard-Block | Speeds up boot by preventing the TPM driver from loading. |
| `8250.nr_uarts=0` | Skip Serial Scan | Disables searching for non-existent legacy RS-232 COM ports. |
| `i915.modeset=1` | Early KMS | Prevents screen "flashing" by initializing the GPU before the desktop loads. |
| `i915.enable_fbc=1` | Framebuffer Compression | Saves power by compressing the image stored in video memory. |
| `i915.enable_psr=1` | Panel Self Refresh | Allows the GPU to enter a low-power "nap" when the screen is static. |
| `i915.enable_guc=3` | GuC/HuC Firmware | Offloads video decoding and power management to dedicated GPU microcontrollers. |
| `pcie_aspm=force` | Aggressive PCIe Power | Forces NVMe and Wi-Fi into low-power states. |
| `nvme_core.default_ps_max_latency_us=5500` | NVMe PS4/PS5 Sleep | Enables deepest SSD sleep states with a "sweet spot" latency for stability. |

> [!IMPORTANT]
> **a)** Blacklisting TPM drivers is recommended for systems using standard Btrfs partitions without LUKS encryption. If you plan to use TPM-based disk unlocking in the future, remove all the TPM entries.
> 
> **b)** On some T480s panels (depending on whether you have the LG or Innolux display), **PSR** can occasionally cause a tiny "stutter" or flicker when moving the mouse after a pause.
If you experience flickering, change the parameter to `i915.enable_psr=0`. It costs about **0.5W** of power but fixes the flicker instantly.
> 
> **c)** Since you are enabling **GuC/HuC** (`i915.enable_guc=3`), the `linux-firmware` package is required, to allow the GPU to handle its own power management and video decoding more efficiently.

&nbsp;

To apply these changes, ensure your kernel command line is updated and you regenerate your initramfs:

**a)** Update `/etc/kernel/cmdline` (or your bootloader's equivalent), and add the following kernel parameters to the end of the file:
  
```
quiet splash loglevel=3 rd.systemd.show_status=auto rd.udev.log_priority=3 tpm_tis.interrupts=0 tpm_tis.force=0 modprobe.blacklist=tpm_tis,tpm_tis_core 8250.nr_uarts=0 i915.modeset=1 i915.enable_fbc=1 i915.enable_psr=1 i915.enable_guc=3 pcie_aspm=force nvme_core.default_ps_max_latency_us=5500
```
  
**b)** Run the following command to rebuild the image and apply these new flags into the UKI.
  
```
sudo mkinitcpio -P
```
  
### 2. Initramfs Configuration (`mkinitcpio.conf`)
  
The `initramfs` is a small environment that loads the hardware drivers needed to mount the root file system. This is an optimized hook set, in order to prioritize stability and boot speed.
  
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

Run the following command to "bake" these hooks into the .efi binary that the BIOS actually executes.
  
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
