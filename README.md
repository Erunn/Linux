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
**intel-ucode:** https://wiki.archlinux.org/title/Microcode  
**python-validity:** https://github.com/uunicorn/python-validity  
**throttled:** https://github.com/erpalma/throttled  
**TLP:** https://linrunner.de/tlp/index.html

```
yay -S bluez intel-media-driver intel-ucode python-validity throttled tlp tlp-rdw tlpui
```

-------------
System Maintenance
-------------
This command will update system and force refresh databases, remove orphaned packages and their unused dependencies, and clean package cache, keeping only installed packages:

```
yay -Syyu --noconfirm && yay -Yc --noconfirm && yay -Sc --noconfirm
```
```
sudo pacman -Syyu && sudo pacman -Rns $(pacman -Qdtq) && sudo pacman -Sc
```
-------------
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
Current power consumption or charge rate
-------------
```
cat /sys/class/power_supply/BAT0/power_now | awk '{print $1 / 1000000 " W"}'
```
