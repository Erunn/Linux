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
Software to Uninstall on Mint
-------------

```
sudo apt purge hypnotix celluloid warpinator thunderbird transmission-gtk transmission-common casper rhythmbox pix webapp-manager
```

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
**timeshift:** https://github.com/linuxmint/timeshift  
**TLP:** https://linrunner.de/tlp/index.html  
**TLPUI:** https://github.com/d4nj1/TLPUI  
**qt5-tools:** https://archlinux.org/packages/extra/x86_64/qt5-tools/  
**libnotify:** https://github.com/GNOME/libnotify  
**qalculate:** https://qalculate.github.io/  
**sddm-conf:** https://aur.archlinux.org/packages/sddm-conf-git  
```
yay -S bluez intel-media-driver intel-ucode python-validity throttled timeshift tlp tlp-rdw tlpui qt5-tools libnotify qalculate-gtk sddm-conf-git
```
> [!IMPORTANT]
> Please refer to documentation to check any necessary following commands that are required to enable and start services.
-------------
Fonts to Install
-------------
**Inter:** https://github.com/rsms/inter  
**IBM Plex® typeface:** https://github.com/IBM/plex

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
```
sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y && sudo apt clean
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
  perc=$(upower -b | awk "/percentage/ {print \$2}");
  time=$(upower -i /org/freedesktop/UPower/devices/battery_BAT0 | awk "/time/ {print \$4, \$5}");
  watt=$(awk "{print \$1 / 1000000 \" W\"}" /sys/class/power_supply/BAT0/power_now);
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
Firefox hardware acceleration 
-------------
Firefox hardware acceleration can help battery life in Linux in a few ways:

- Offloading tasks to the GPU: Hardware acceleration allows Firefox to utilize the computer's graphics processing unit (GPU) for specific tasks like rendering web pages, decoding videos, and playing animations. GPUs are often more efficient at handling these tasks than the main processor (CPU). This offloading reduces the overall workload on the CPU, which may lead to lower power consumption and improved battery life.

- Improved rendering efficiency: Modern GPUs are specifically designed to optimize graphics-intensive tasks. Hardware acceleration enables Firefox to leverage these optimizations, leading to faster and smoother rendering of web content. Efficient rendering may require less computational power from both the CPU and the GPU, potentially improving battery life.

- Dedicated video decoding: GPUs often include specialized hardware for decoding video content, which can be significantly more power-efficient than software-based decoding on the CPU. By utilizing hardware acceleration, Firefox can offload video decoding to the GPU, potentially improving battery life during video playback.
Sources

**Sources:** https://community.frame.work/t/tracking-linux-battery-life-tuning/6665

## 1. Environment Variables
| Variable | Value | Battery Benefit |
|---------|-------|----------------|
| `MOZ_X11_EGL` (Xorg) | 1 | Uses GPU for rendering instead of CPU, reducing CPU load and battery drain. |
| `MOZ_ENABLE_WAYLAND` (Wayland) | 1 | Enables native Wayland rendering for more efficient GPU usage, lowering energy consumption. |

## 2. Hardware Video Acceleration
| Flag | Value | Battery Benefit |
|------|-------|----------------|
| `media.ffmpeg.vaapi.enabled` | true | Offloads video decoding to GPU, reducing CPU usage and saving battery during video playback. |
| `media.hardware-video-decoding.enabled` | true | GPU-based video decoding consumes less power than CPU decoding, improving battery life. |
| `media.hardware-video-decoding.force-enabled` | false | Avoids forcing GPU decoding on unsupported hardware, preventing extra CPU usage and wasted energy. |

## 3. RDD / GPU Process
| Flag | Value | Battery Benefit |
|------|-------|----------------|
| `media.rdd-ffmpeg.enabled` | true | Moves video decoding to GPU process, lowering CPU workload and power consumption. |
| `media.rdd-vpx.enabled` | true | Uses GPU for VP8/VP9 decoding, reducing CPU cycles and battery usage. |

## 4. VPX Codec (VP8/VP9)
| Flag | Value | Battery Benefit |
|------|-------|----------------|
| `media.ffvpx.enabled` | true | Software fallback avoids CPU spikes and inefficient processing, conserving battery. |
| `media.navigator.mediadatadecoder_vpx_enabled` | true | Offloads VP8/VP9 decoding in video calls to GPU, reducing CPU power draw. |

## 5. Wayland DMA Buffers
| Flag | Value | Battery Benefit |
|------|-------|----------------|
| `widget.wayland-dmabuf-vaapi.enabled` | true | Zero-copy video rendering reduces CPU/memory usage, saving battery. |
| `widget.wayland-dmabuf-webgl.enabled` | true | Efficient WebGL rendering lowers CPU/GPU workload and power consumption. |

## 6. WebRender
| Flag | Value | Battery Benefit |
|------|-------|----------------|
| `gfx.webrender.all` | true | GPU-accelerated page rendering reduces CPU cycles, lowering battery usage. |

## 7. FFmpeg & DMA Buffers
| Flag | Value | Battery Benefit |
|------|-------|----------------|
| `media.ffmpeg.dmabuf-textures.enabled` | true | Zero-copy textures reduce CPU-intensive memory copying, conserving energy. |

## 8. Video Codec Settings
| Flag | Value | Battery Benefit |
|------|-------|----------------|
| `media.av1.enabled` | false | AV1 decoding is CPU-heavy; disabling it reduces battery drain on devices without AV1 hardware acceleration. |


