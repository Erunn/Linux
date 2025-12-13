# Linux

**BIOS Configs**  
-------------

**Config -> Thunderbolt (TM) 3**

Thunderbolt BIOS Assist Mode - > Disabled

**Config -> Network**

Wake On LAN - > Disabled  
Wake On LAN from Dock - > Disabled

**Security -> I/O Port Access**

Ethernet LAN - > Disabled  
Wireless WAN - > Disabled  
Memory Card Slot - > Disabled  
Smart Card Slot - > Disabled  
Thunderbolt (TM) 3 - > Disabled  

-------------
Software to Install
-------------
`yay` (Yet Another Yogurt) is a popular and widely used AUR helper for Arch Linux.  
It automates and streamlines the process of interacting with the AUR, making it much easier to install, update, and manage packages from both the official repositories and the AUR. It acts as a wrapper around pacman.  

**yay:** https://github.com/Jguer/yay
```
sudo pacman -S --needed git base-devel && git clone https://aur.archlinux.org/yay.git && cd yay && makepkg -si
```
After installing
```
yay -Y --gendb
```
You can now use `yay` to install the following software:

| Name | Description | Installation Command |
| :--- | :--- | :--- |
| **bluez** | The official **Linux Bluetooth protocol stack** (the core framework for wireless communication). | `yay -S bluez` |
| **blueman** | A **GTK+ Bluetooth Manager** (GUI) that simplifies managing and pairing Bluetooth devices. | `yay -S blueman` |
| **featherpad** | A **lightweight, independent Qt text editor** with minimal dependencies and syntax highlighting. | `yay -S featherpad` |
| **fwupd** | A daemon to manage the installation of **firmware updates** (UEFI/Capsule) for connected hardware. | `yay -S fwupd` |
| **gvfs** | The **GNOME Virtual File System** that provides access to remote filesystems (SFTP, FTP, SMB) and local devices. | `yay -S gvfs` |
| **intel-ucode** | **Intel processor microcode updates** that provide critical bug fixes and security updates for the CPU. | `yay -S intel-ucode` |
| **intel-undervolt** | A utility for **undervolting Intel CPUs** under Linux to reduce heat and improve power efficiency. | `yay -S intel-undervolt` |
| **labwc** | A **minimalist window-stacking Wayland compositor** (inspired by Openbox). The core of the desktop. | `yay -S labwc` |
| **labwc-tweaks** | A **Qt-based GUI configuration tool** for managing `labwc` settings and themes. | `yay -S labwc-tweaks-git` |
| **kanshi** | A dynamic output configuration manager for Wayland that **automatically switches display profiles**. | `yay -S kanshi` |
| **lxqt-wayland-session** | Provides the **necessary scripts and files** to launch an LXQt or compatible session under Wayland. | `yay -S lxqt-wayland-session` |
| **nm-tray** | A simple **Qt-based NetworkManager frontend** that resides in the system tray for network access. | `yay -S nm-tray` |
| **qalculate** | A versatile, cross-platform **desktop calculator** (Qt version) with symbolic calculation and unit conversion. | `yay -S qalculate-qt` |
| **qt5-declarative** | Development files for **Qt 5's QML/declarative framework**, often a dependency for Qt-based apps. | `yay -S qt5-declarative` |
| **stress-ng** | A tool to **stress test** a computer system (CPU, memory, I/O) for stability and debugging. | `yay -S stress-ng` |
| **TLP** | A feature-rich **command-line power management tool** for optimizing laptop battery life. | `yay -S tlp tlp-rdw` |
| **TLPUI** | A **GTK graphical user interface** for easily configuring the TLP power management settings. | `yay -S tlpui` |
| **wdisplays** | A **graphical utility** for configuring display outputs (resolution, position, rotation) on Wayland. | `yay -S wdisplays` |

> [!IMPORTANT]
> Please refer to documentation to check any necessary following commands that are required to enable and start services.
-------------
Fonts to Install
-------------
**Inter:** https://github.com/rsms/inter  

```
yay -S inter-font
```

System Maintenance
-------------
This command will update system and force refresh databases, remove orphaned packages and their unused dependencies, and clean package cache, keeping only installed packages:

```
yay -Syyu --noconfirm && yay -Yc --noconfirm && yay -Sc --noconfirm
```
```
sudo pacman -Syyu && sudo pacman -Rns $(pacman -Qdtq) && sudo pacman -Sc
```

Analyzing the boot proces
-------------

```
sudo systemd-analyze
```

```
sudo systemd-analyze blame
```

```
sudo systemd-analyze critical-chain
```

-------------
Check The System For Errors
-------------
System service errors are common and should be checked frequently to know what is broken and needs fixing:

```
sudo systemctl --failed
```
You can check log files for system errors as well:
```
sudo journalctl -p 3 -xb
```

-------------
Taming the journal's size
-------------

Systemd's system journal's size can go out of control. There are some things you can do to keep it in control:

```
sudo journalctl --vacuum-size=100M
sudo journalctl --vacuum-time=2weeks
```

-------------
Enable SSD trim
-------------

For optimal performance and longevity of your SSD, you should enable the fstrim.timer. This will ensure the TRIM command is run weekly, which tells your SSD which data blocks are no longer in use and can be erased.
```
sudo systemctl enable --now fstrim.timer
```
After running the previous command, check the status. It should show the timer is active and indicate the next time the service will run.
```
sudo systemctl status fstrim.timer
```
-------------
Custom Command Widget - Battery stats
-------------
```
sh -c '
  BAT_SYS_PATH="/sys/class/power_supply/BAT0"
  BAT_UPOWER_PATH="/org/freedesktop/UPower/devices/battery_BAT0"
  watt=$(awk "{printf \"%.2f W\", \$1 / 1000000}" "$BAT_SYS_PATH/power_now")
  perc=$(cat "$BAT_SYS_PATH/capacity")%
  time=$(upower -i "$BAT_UPOWER_PATH" | awk "/time/ {print \$4, \$5}")
  echo "$perc | $time | $watt |"
'
```

-------------
Keeping the boot output minimal.
-------------
**Reference:** https://wiki.archlinux.org/title/Kernel_parameters  

Doing this will suppresses most kernel messages from being displayed on the console during boot.  
This results in a cleaner, less verbose boot process, since only messages with a priority level of 3 (errors) or higher will be printed to the console.

Go to  `/efi/loader/entries/` and add `rw quiet splash loglevel=3` at the end of your .conf file.

-------------
Environment Variables
-------------
| Variable | Value | Purpose |
| :--- | :--- | :--- |
| `MOZ_ENABLE_WAYLAND` | `1` | Forces Firefox to use the native Wayland backend for maximum speed and efficiency. |
| `MOZ_VA_API_ALL_DRIVERS` | `1` | Forces Firefox VA-API hardware video decoding, drastically reducing CPU load and saving battery during video playback. |
| `MOZ_ENABLE_DMABUF` | `1` | Enables Firefox Direct Memory Access Buffer sharing, reducing memory copies for better rendering efficiency. |
| `MOZ_ACCELERATE_VSYNC` | `1` | Forces Firefox VSync to be handled by the Wayland compositor for smoother, more efficient rendering. |
| `MOZ_DRM_DISABLE_PRIME` | `1` | Improves Firefox stability by disabling multi-GPU offloading logic on integrated-only systems. |
| `QT_QPA_PLATFORM` | `wayland` | Forces Qt applications to use the native Wayland protocol instead of XWayland. |
| `GDK_BACKEND` | `wayland,x11` | Tells GTK applications to prefer the native Wayland backend. |


systemctl list-units --type=service --state=running
systemctl list-timers
systemctl --user list-units --type=service --state=running


Stop and Mask the main AT-SPI D-Bus service:
Bash

systemctl --user mask at-spi-dbus-bus.service
systemctl --user stop at-spi-dbus-bus.service

sudo systemctl stop avahi-daemon.service
sudo systemctl disable avahi-daemon.service

Stop and Mask the related Registry service:
Bash

systemctl --user mask org.a11y.atspi.Registry.service
systemctl --user stop org.a11y.atspi.Registry.service

systemctl --user stop dbus-:1.24-org.a11y.atspi.Registry@0.service

Things to uninstall

sudo pacman -Rns network-manager-applet l3afpad avahi



### Configuration File Content
