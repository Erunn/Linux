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

**python-validity:** https://github.com/uunicorn/python-validity  
**throttled:** https://github.com/erpalma/throttled  
**TLP:** https://linrunner.de/tlp/index.html


```yay -S intel-media-driver python-validity throttled tlp tlp-rdw tlpui```

```sudo systemctl enable --now throttled.service```

```fprintd-enroll```

-------------
Analyzing the boot proces
-------------

sudo systemd-analyze  
sudo systemd-analyze blame  
sudo systemd-analyze critical-chain  
