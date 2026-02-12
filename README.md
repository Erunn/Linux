# 🚀 Arch Linux Minimal & Optimized Setup (T480s)

This repository serves as a comprehensive guide for configuring a high-performance, power-efficient, and lightweight Arch Linux environment. Tuned specifically for a modified **ThinkPad T480s**, these optimizations focus on achieving a sub-4W idle draw while maintaining a modern Wayland-based workflow.

### 🎯 Project Goals
* **Maximizing Battery:** Leveraging advanced kernel parameters (ASPM, PSR, GuC) and hardware-level hardening to reach extreme battery runtimes.
* **Modern Wayland Stack:** Using **LabWC** and **LXQt** for a footprint significantly lighter than GNOME or KDE.
* **Instant Responsiveness:** Optimized memory management via **ZRAM** and a streamlined **initramfs** for near-instant boot times.
* **Minimalist Maintenance:** A "zero-bloat" approach to system updates and package management.

### 💰 Build Value & Cost Breakdown
| Item | Description | Cost |
| :--- | :--- | :--- |
| **ThinkPad T480s** | i5-8250U, 16GB RAM, 256GB SSD | 229,80€ |
| **Panel Upgrade** | AUO B140HAN06.B (400 nits, 100% sRGB) | 83,48€ |
| **Glass Trackpad** | X1 Extreme / P1 Glass Trackpad | 21,17€ |
| **Wi-Fi 6E Mod** | Intel AX210 Gig+ Module | 15,79€ |
| **Total Build** | --- | **350,24€** |

---

## 📖 Table of Contents
1. [🔒 BIOS / Firmware Configuration](#-bios--firmware-configuration)
2. [🛠️ Installation & Setup](#-installation--setup)
3. [🧠 Memory Management](#-memory-management)
4. [🗄️ Btrfs & SSD Optimization](#-btrfs--ssd-optimization)
5. [⚡ System & Power Optimization](#-system--power-optimization)
6. [🖥️ User Experience & Boot](#-user-experience--boot)
7. [💻 System Maintenance](#-system-maintenance)
8. [🔍 Analysis & Debugging](#-analysis--debugging)

---
  
## 🔒 BIOS / Firmware Configuration
  
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
## 🧠 Memory Management

Since this setup utilizes Btrfs on an NVMe SSD, ZRAM is used instead of a traditional swap partition/file. This creates a compressed swap device in RAM, which is significantly faster than swapping to disk and extends the lifespan of the SSD.

**Disable ZSwap**

ZSwap is a kernel feature that acts as a cache for a disk-based swap. Since we are using ZRAM, ZSwap is redundant and can be disabled via the kernel parameter included in your cmdline (check `/etc/kernel/cmdline`):

```
zswap.enabled=0
```

**Install and Configure zram-generator**

`zram-generator` is written in Rust, extremely lightweight, and integrates directly with systemd to manage ZRAM devices.

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

> [!IMPORTANT]
> For systems with 8 GB RAM, consider ram / 3 instead of ram / 2.

**Tuning Swappiness & VM Parameters**

To ensure the system uses the compressed ZRAM effectively before touching the disk, create or edit `/etc/sysctl.d/99-vm-zram-parameters.conf`,  and add the following:

```
vm.swappiness = 180
vm.watermark_boost_factor = 0
vm.watermark_scale_factor = 125
vm.page-cluster = 0
```

**Verification**

After a reboot, verify that your ZRAM is active and using the `zstd` algorithm:

```
zramctl
```

`/dev/zram0` shall be listed with the total swap size and the current compression ratio.

---

## 🗄️ Btrfs & SSD Optimization

Since this build uses Btrfs on an NVMe SSD, the filesystem is tuned to reduce "write amplification" and allow the CPU to maintain deeper sleep states by grouping disk activity.

### 1. Optimized Mount Options (fstab)

These settings reduce background disk noise and utilize Level 1 compression for the best balance of speed vs. battery life.

| Option | Function | Benefit |
| :--- | :--- | :--- |
| **`rw`** | Read-Write Mount | Ensures the filesystem is writable immediately upon mounting. |
| **`noatime`** | Disable Access Time | Stops the SSD from writing metadata every time a file is read. |
| **`compress=zstd:1`** | Transparent Compression | Uses Level 1 compression for the lowest CPU overhead during disk IO. |
| **`ssd`** | Flash Optimization | Ensures Btrfs block allocation is optimized for NVMe storage. |
| **`commit=120`** | Delayed Write-Buffer | Groups writes every 120s, allowing the CPU to stay idle longer. |
| **`space_cache=v2`** | Modern Free-Space Tracking | Improves performance and reduces mount times on modern NVMe drives. |

To apply this, edit `/etc/fstab`, and replace your current mount options with the following, so they look like the following example (for
```
# Btrfs Subvolumes (Optimized)
UUID=your-uuid  /          btrfs  rw,noatime,compress=zstd:1,ssd,commit=120,subvol=@     0 0
UUID=your-uuid  /home      btrfs  rw,noatime,compress=zstd:1,ssd,commit=120,subvol=@home 0 0

# Boot Partition (Standard VFAT)
UUID=boot-uuid  /boot      vfat   rw,noatime,fmask=0137,dmask=0027,utf8                  0 2
```

> [!NOTE]
> **a)** The discard mount `flag discard=async` is intentionally omitted. Modern Linux setups prefer "Batch TRIM" via a systemd timer to prevent the constant background "chatter" of continuous discards.
>
> **b)** For Btrfs partitions with multiple subvolumes (e.g., @, @home, @log), you must apply the all optimization flags (compress, commit, noatime, etc.) to every single line that uses that UUID.For VFAT partitions, only apply the `noatime` flag.


### 1. Maintenance

To ensure the SSD performs maintenance efficiently, we use a weekly batch TRIM instead of continuous discards. To enable it, run:

```
sudo systemctl enable --now fstrim.timer
```
---

## ⚡ System & Power Optimization

This section focuses on reducing the energy footprint of the software stack and maximizing the hardware efficiency of the Intel and networking components.
  
### 1. Wayland Environment Variables
  
Set these environment variables in your LXQt Session Settings for maximum performance and battery savings. These ensure that applications use native Wayland protocols and hardware acceleration instead of inefficient X11 translation.
  
| Variable | Value | Purpose |
| :--- | :--- | :--- |
| **`MOZ_ENABLE_WAYLAND`** | `1` | Forces Firefox to use native Wayland protocols, enabling zero-copy screen updates. |
| **`MOZ_WEBRENDER`** | `1` | Offloads page "painting" to the GPU; keeps CPU in deep C-states during web interaction. |
| **`MOZ_VA_API_ALL_DRIVERS`** | `1` | Ensures hardware video decoding is prioritized regardless of driver versioning. |
| **`MOZ_ENABLE_DMABUF`** | `1` | Allows video textures to stay in GPU memory, avoiding expensive CPU/RAM copies. |
| **`MOZ_ACCELERATE_VSYNC`** | `1` | Prevents "over-rendering" by syncing frames perfectly with the 60Hz display. |
| **`LIBVA_DRIVER_NAME`** | `iHD` | Explicitly targets the modern Intel Media Driver for the 8th Gen UHD 620 graphics. |
| **`QT_QPA_PLATFORM`** | `wayland` | Forces Qt apps to use Wayland, avoiding the power overhead of XWayland translation. |
| **`GDK_BACKEND`** | `wayland` | Ensures GTK apps bypass legacy X11 protocols for lower input latency and power. |
| **`GTK_MODULES`** | `""` | Stops GTK from loading unused legacy modules that cause unnecessary background wakeups. |
| **`NO_AT_BRIDGE`** | `1` | Hard-disables the accessibility bus, saving CPU cycles spent on background IPC polling. |

### 2. Wireless Networking with iwd (iNet Wireless Daemon)

Replacing `wpa_supplicant` with `iwd` results in faster connection times, lower memory footprint, and cleaner roaming. It significantly improves "Resume from Sleep" speed.

**Configure NetworkManager to use iwd:**

To use `iwd` as the backend for **NetworkManager**, create or edit `/etc/NetworkManager/conf.d/wifi_backend.conf` and add:

```
[device]
wifi.backend=iwd
wifi.iwd.autoconnect=yes
```

Edit `/etc/iwd/main.conf` as well, and add this:

```
[General]
EnableNetworkConfiguration=true

[Scan]
DisablePeriodicScan=true

[Power]
PowerSave=true
```

To prevent conflicts, disable the old `wpa_supplicant` service and enable the new `iwd` one:

```
sudo systemctl mask --now wpa_supplicant.service
sudo systemctl enable --now iwd.service
```

Restart NetworkManager and iwd to apply changes:

```
sudo systemctl restart NetworkManager
sudo systemctl restart iwd
```

**iwd Cheat Sheet (iwctl)**

Quick scan and list:

```
iwctl station wlan0 scan && iwctl station wlan0 get-networks
```

Connect to your home Wi-Fi instantly:

```
iwctl --passphrase "YOUR_PASSWORD" station wlan0 connect "YOUR_SSID"
```

### 3. CPU Undervolting (`intel-undervolt`)

Lowering the voltage on the i5-8250U reduces heat and prevents thermal throttling.

**Configuration:**

Edit `/etc/intel-undervolt.conf`, and change the values:

```
undervolt 0 'CPU' -95
undervolt 1 'GPU' -60
undervolt 2 'CPU Cache' -95
undervolt 3 'System Agent' -60
undervolt 4 'Analog I/O' 0
```

**Apply and enable:**

```
sudo intel-undervolt apply
sudo systemctl enable --now intel-undervolt.service
```

> [!IMPORTANT]
>Please note that voltage tolerance varies between individual chips (the "silicon lottery"). The values provided in this guide may not be stable for every system.
>After applying new undervolt settings, it is highly recommended to run `stress-ng` to verify system stability under load. If you experience crashes or hangs, reduce the undervolt offset (e.g., move from -100mV to -80mV) until the system is stable.

### 4. Systemd: Security & Hardware Masking

Masking services prevents systemd from waiting for unnecessary hardware to initialize.

**Disable delays caused by TPM polling and Network waiting**

Masking these services prevents systemd from waiting for the TPM 2.0 chip to initialize, which can shave significant time off your boot process.

```
sudo systemctl mask \
systemd-pcrphase.service \
systemd-pcrphase-sysinit.service \
systemd-pcrphase-initrd.service \
systemd-pcrproduct.service \
systemd-pcrmachine.service \
systemd-pcrnvdone.service
```

> [!IMPORTANT]
> **TPM & Measured Boot Masking**
> The masking of `systemd-pcr*` and `systemd-tpm2*` services is intended for systems **not using LUKS encryption** or TPM-based disk unlocking. On this specific build, these are disabled to eliminate the 5+ second software delay caused by the system waiting for the TPM chip to initialize and measure the boot stages. 
> 
> *If you plan to use TPM-based full disk encryption in the future, these services must remain unmasked.*

**Disable delays caused by Network waiting**
  
By default, some systems wait for a confirmed internet connection before reaching the login manager. Disabling this service allows the desktop to load while Wi-Fi connects in the background.
  
```
sudo systemctl mask NetworkManager-wait-online.service
sudo systemctl mask systemd-networkd-wait-online.service
```

**User-Level Service Masking (Noise Reduction)**

To minimize CPU wakeups and SSD metadata flushes, we mask these non-essential user services:
  
```
systemctl --user mask at-spi-dbus-bus.service
systemctl --user mask org.a11y.atspi.Registry.service
systemctl --user mask gvfs-metadata.service
systemctl --user stop at-spi-dbus-bus.service
systemctl --user stop org.a11y.atspi.Registry.service
systemctl --user stop gvfs-metadata.service
```

**Bluetooth "Auto-On" Disable**

To prevent Bluetooth from wasting battery by turning on at every boot, edit `/etc/bluetooth/main.conf` and set: 

```
AutoEnable=false
```
> [!IMPORTANT]
> If it has a # at the beginning of the line, just delete the # to "uncomment" it.

---

## 🖥️ User Experience & Boot

### 1. Optimized Kernel Boot Parameters

The Linux kernel’s behavior at startup is governed by boot-time parameters. For this build, the command line has been tuned for three primary objectives: 

**Look Better:** Stops the "text wall" and flashing during boot so you get a clean, smooth transition to your desktop.

**Boot Faster:** Tells the computer to stop searching for old hardware that the T480s doesn't even have (like old serial ports).

**Save Battery:** Forces your hardware (SSD, Wi-Fi, and GPU) to take "micro-naps" whenever you aren't moving the mouse, which adds up to significantly more battery life.
  
| Parameter | Function | Benefit |
| :--- | :--- | :--- |
| **`quiet`** | Minimal Boot Verbosity | Removes the "wall of text" during startup for a cleaner look. |
| **`splash`** | Boot Splash Support | Allows the use of a graphical boot logo (BGRT). |
| **`loglevel=3`** | Error Suppression | Filters out non-critical kernel "noise" while keeping errors visible. |
| **`rd.systemd.show_status=auto`** | Smart Service Status | Only shows systemd service logs if a service fails to start. |
| **`rd.udev.log_priority=3`** | Udev Verbosity Control | Hides hardware initialization logs for a seamless boot transition. |
| **`zswap.enabled=0`** | Disable Zswap | Prevents kernel-level swap compression (Optimized for ZRAM setups). |
| **`tpm.disable=1`** | TPM Kernel Killswitch | Disables the TPM subsystem to prevent background hardware polling. |
| **`8250.nr_uarts=0`** | Skip Serial Scan | Disables searching for non-existent legacy RS-232 serial ports. |
| **`i915.modeset=1`** | Early KMS | Prevents screen "flashing" by initializing GPU before the login manager. |
| **`i915.enable_fbc=1`** | Framebuffer Compression | Reduces memory bandwidth and power by compressing video memory. |
| **`i915.enable_psr=1`** | Panel Self Refresh | Stops GPU refresh cycles when the image is static (Huge idle savings). |
| **`i915.psr_safest_params=1`** | PSR Stability Guard | Uses conservative timing to prevent flickering on 8th Gen panels. |
| **`i915.enable_guc=2`** | HuC Authentication | Offloads HEVC/VP9 video decoding to the GPU micro-engine. |
| **`i915.enable_dc=4`** | Display Power C-States | Enables deepest DC5/DC6 states. |
| **`pcie_aspm=force`** | Aggressive PCIe Power | Forces NVMe, Wi-Fi, and LAN into deepest L1.2 low-power states. |
| **`transparent_hugepage=never`** | Disable Huge Pages | Stops background memory compaction wakeups; better for ZRAM/Btrfs. |
| **`nvme_core.default_ps_max_latency_us=8000`** | NVMe Deep Sleep | Allows SSD to enter PS 4 (4mW) state (Specific to my drive's 8ms exit latency). |
| **`intel_pstate=passive`** | CPU Driver Mode | Unlocks the `schedutil` governor for smoother, kernel-aware scaling. |
| **`intel_idle.max_cstate=10`** | Force Deep C-States | Unlocks the CPU's ability to reach ultra-deep C9/C10 sleep levels. |
| **`mei.enable=0`** | Intel ME Interface Disable | Prevents the Management Engine from "vetoing" deep Package C-states. |
| **`nmi_watchdog=0`** | Disable NMI Watchdog | Stops the periodic kernel "heartbeat" interrupt, reducing CPU wakeups. |
| **`mem_sleep_default=deep`** | S3 Deep Sleep | Forces traditional S3 'Deep' sleep instead of Modern Standby (S2idle). |

> [!IMPORTANT]
> **a)** Blacklisting TPM drivers is recommended for systems using standard Btrfs partitions without LUKS encryption. If you plan to use TPM-based disk unlocking in the future, remove all the TPM entries.
> 
> **b)** On some T480s panels (depending on whether you have the LG or Innolux display), **PSR** can occasionally cause a tiny "stutter" or flicker when moving the mouse after a pause.
If you experience flickering, change the parameter to `i915.enable_psr=0`.
> 
> **c)** To enable **GuC/HuC** (`i915.enable_guc=2`), the `linux-firmware` package is required, to allow the GPU to handle its own power management and video decoding more efficiently.

To apply these changes, ensure your kernel command line is updated and you regenerate your initramfs:

**a)** Update `/etc/kernel/cmdline` (or your bootloader's equivalent), and add the following kernel parameters to the end of the file:
  
```
quiet splash loglevel=3 rd.systemd.show_status=auto rd.udev.log_priority=3 tpm.disable=1 8250.nr_uarts=0 i915.modeset=1 i915.enable_fbc=1 i915.enable_psr=1 i915.psr_safest_params=1 i915.enable_guc=2 i915.enable_dc=4 pcie_aspm=force transparent_hugepage=never nvme_core.default_ps_max_latency_us=8000 intel_pstate=passive intel_idle.max_cstate=10 mei.enable=0 nmi_watchdog=0 mem_sleep_default=deep
```
  
**b)** Run the following command to rebuild the image and apply these new flags into the UKI.
  
```
sudo mkinitcpio -P
```
### 2. Initramfs Configuration

The initramfs is the environment that loads hardware drivers. Using the systemd hook allows for faster, parallelized initialization compared to the legacy base hook.
    
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

Edit `/etc/mkinitcpio.conf` and edit the modules and hooks lines, to look like this:

```
MODULES=(i915)  
```

```
HOOKS=(microcode systemd autodetect modconf kms keyboard sd-vconsole block filesystems)
```

Regenerate the image:
  
```
sudo mkinitcpio -P
```

### 3. Custom Command Widget - Battery Stats

This command provides detailed battery statistics (percentage, remaining time, and power draw in Watts) for use in status bars or custom widgets.
  
```
awk -F= '/ENERGY_NOW/{en=$2/1e6} /ENERGY_FULL_DESIGN|ENERGY_FULL/{ef=$2/1e6} /POWER_NOW/{p=$2/1e6} /CAPACITY=/{c=$2} /STATUS/{s=$2} END {icon=(s=="Charging"?"":""); t_icon=""; if(s=="Charging") {h=(p>0.1?(ef-en)/p:0)} else if(s=="Full") {h=0} else {h=(p>0.1?en/p:0)}; if(h>20)h=0; hr=int(h); min=int((h-hr)*60); if(s=="Full") t_val="Full"; else t_val=(hr>0?sprintf("%02dh %02dm",hr,min):sprintf("%02dm",min)); printf "%s %d%% | %s %s |  %.2f W\n", icon, c, t_icon, t_val, p}' /sys/class/power_supply/BAT0/uevent
```

---

## 💻 System Maintenance

To maintain the "Minimalist" nature of this build, these maintenance tasks ensure that the SSD remains fast, logs don't bloat the system, and the package cache stays clean.

### 1. Full System Update & Cleanup
This comprehensive one-liner handles the entire maintenance cycle: it updates the system, removes "orphaned" packages (dependencies no longer needed), and clears the package cache.

```
sudo pacman -Sc --noconfirm && yay -Syu --noconfirm && yay -Yc --noconfirm && yay -Sc --noconfirm
```

### 2. Enable SSD TRIM
  
Since this build uses an NVMe SSD with Btrfs, regular TRIM operations are essential to maintain write speeds and drive longevity. We use the systemd timer to handle this weekly.

**Enable the weekly TRIM service:** 

```
sudo systemctl enable --now fstrim.timer
```
**Check status to ensure it is scheduled:**
  
```
sudo systemctl status fstrim.timer
```

### 3. Taming the Journal Size
  
By default, systemd logs can grow to several gigabytes. For a minimal setup, we limit the journal to a small footprint and a 2-week history.

Execute the command:

```
sudo nano /etc/systemd/journald.conf
```

Update (or uncomment) the following lines:

```
SystemMaxUse=100M
MaxRetentionSec=2week
```
  
### 4. Cleaning Up Redundant Packages
  
Uninstall legacy or heavyweight utilities to keep the system optimized for the Wayland/LXQt stack:

```
sudo pacman -Rns network-manager-applet l3afpad vim
```

---
  
## 🔍 Analysis & Debugging

Use these commands to verify that your optimizations (ZRAM, Boot speed, etc.) are working as intended.

### 1. Analyzing the Boot Process
  
Check and debug slow boot times:
  
```
echo "=== SUMMARY ==="; systemd-analyze; echo -e "\n=== FULL BLAME ==="; systemd-analyze blame --no-pager; echo -e "\n=== CRITICAL CHAIN ==="; systemd-analyze critical-chain --no-pager
```
  
### 2. Check for System Errors
  
A quick way to check kernel health, power management status, and ZRAM compression in one go:
  
```
echo "--- KERNEL ERRORS ---"; sudo journalctl -p 3 -xb --no-hostname --no-pager; echo -e "\n--- POWER & VOLTAGE ---"; sudo journalctl -u tlp -u intel-undervolt -b --no-pager; echo -e "\n--- BTRFS HEALTH ---"; sudo journalctl -t btrfs -b --no-pager; echo -e "\n--- ZRAM STATUS ---"; zramctl
```
  
### 3. Running Services and Timers
  
Monitor what is currently running to ensure no unnecessary background bloat has crept in:
  
```
echo "=== ACTIVE SYSTEM SERVICES ==="; systemctl list-units --type=service --state=active --no-pager; echo -e "\n=== ACTIVE USER SERVICES ==="; systemctl --user list-units --type=service --state=active --no-pager; echo -e "\n=== SYSTEM TIMERS ==="; systemctl list-timers --no-pager; echo -e "\n=== FAILED UNITS ==="; systemctl --failed --no-pager
```

---

## 🎮 Gaming & eGPU Optimization

This section focuses on enabling high-performance gaming via a Thunderbolt 3 eGPU (Nvidia) while maintaining the system's stability and low power consumption when on the move. The configuration uses a **Hybrid Graphics** approach: the Intel iGPU handles the lightweight Wayland desktop, while the Nvidia eGPU is summoned only for heavy 3D workloads.

### 1. Enable 32-bit Support (Multilib)

Steam and many Wine/Proton games rely on 32-bit libraries. You must enable the `multilib` repository before installing drivers.

Edit `/etc/pacman.conf`:





# Example line in /etc/mkinitcpio.conf
MODULES=(i915 nvidia nvidia_modeset nvidia_uvm nvidia_drm)

echo "blacklist nouveau" | sudo tee /etc/modprobe.d/blacklist-nouveau.conf

sudo pacman -S nvidia-dkms nvidia-utils lib32-nvidia-utils nvidia-settings egl-wayland

# Example line in /etc/mkinitcpio.conf
MODULES=(i915 nvidia nvidia_modeset nvidia_uvm nvidia_drm)

To enable multilib:

    sudo featherpad /etc/pacman.conf

    Find the lines for [multilib] and uncomment them (remove the #):
    Code snippet

    [multilib]
    Include = /etc/pacman.d/mirrorlist

    Save and run: sudo pacman -Syu

    # 1. Install the missing headers
sudo pacman -S linux-headers

# 2. Trigger the driver build manually (this might take a minute)
sudo dkms autoinstall

# 3. Now run your mkinitcpio command again
sudo mkinitcpio -P

cmd line

nvidia-drm.modeset=1

