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

**intel-ucode:** https://wiki.archlinux.org/title/Microcode  
**python-validity:** https://github.com/uunicorn/python-validity  
**throttled:** https://github.com/erpalma/throttled  
**TLP:** https://linrunner.de/tlp/index.html

```
yay -S intel-media-driver intel-ucode python-validity throttled tlp tlp-rdw tlpui
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
