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

**bluez:** https://wiki.archlinux.org/title/Bluetooth  
**blueman:** https://github.com/blueman-project/blueman  
**gvfs:** https://gitlab.gnome.org/GNOME/gvfs  
**intel-ucode:** https://wiki.archlinux.org/title/Microcode  
**intel-undervolt:** https://github.com/kitsunyan/intel-undervolt  
**libnotify:** https://github.com/GNOME/libnotify  
**nm-tray:** https://github.com/palinek/nm-tray   
**qalculate:** https://qalculate.github.io/   
**qt5-tools:** https://archlinux.org/packages/extra/x86_64/qt5-tools/  
**stress-ng:** https://github.com/ColinIanKing/stress-ng  
**TLP:** https://linrunner.de/tlp/index.html  
**TLPUI:** https://github.com/d4nj1/TLPUI  

```
yay -S bluez blueman gvfs intel-ucode intel-undervolt libnotify nm-tray-git qalculate-qt qt5-tools stress-ng tlp tlp-rdw tlpui
```
> [!IMPORTANT]
> Please refer to documentation to check any necessary following commands that are required to enable and start services.
-------------
Fonts to Install
-------------
**Inter:** https://github.com/rsms/inter  

```
yay -S inter-font ttf-ibm-plex
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
Check SSD trim
-------------
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
| Variable | Value | Battery Benefit |
|---------|-------|----------------|
| `MOZ_X11_EGL` (Xorg) | 1 | Uses GPU for rendering instead of CPU, reducing CPU load and battery drain. |
| `MOZ_ENABLE_WAYLAND` (Wayland) | 1 | Enables native Wayland rendering for more efficient GPU usage, lowering energy consumption. |
