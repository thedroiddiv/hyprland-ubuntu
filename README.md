# System conf
### HP Pavillion Gaming 15-xx
- CPU: AMD Ryzen 5600H
- GPU1:
- GPU2: NVIDIA RTX 3050 Mobile (NV177)

### Operating System
- Ubuntu 23.10 (Mantic Minotaur)
- Kernel: 6.5.0-14-generic

# NVIDIA Only
### Installing Nvidia Drivers (Closed Source)
```bash
sudo apt -y update
sudo apt -y upgrade
sudo ubuntu-drivers install
```
### Adding DRM params  
- add `nvidia_drm.modeset=1` to `/etc/default/grub` in `GRUB_CMDLINE_LINUX_DEFAULT=`
```bash
GRUB_CMDLINE_LINUX_DEFAULT='quiet splash nvidia_drm.modeset=1
```

# Installing Hyprland
Either run the script or follow step-by-step
TODO: Step by Step process

# Post-Install Config (NVIDIA Only)
Add these env vars for `.config/hypr/hyprland.conf`
```bash
env = LIBVA_DRIVER_NAME,nvidia
env = XDG_SESSION_TYPE,wayland
env = GBM_BACKEND,nvidia-drm
env = __GLX_VENDOR_LIBRARY_NAME,nvidia
env = WLR_NO_HARDWARE_CURSORS,1
```
If Hyprland crashes on launch, then remove `env = GBM_BACKEND,nvidia-drm`

[Source](https://wiki.hyprland.org/Nvidia/)

## Scripts based on
[Script](https://gist.github.com/Vertecedoc4545/3b077301299c20c5b9b4db00f4ca6000)
[Nvidia Patch](https://gist.github.com/Vertecedoc4545/07a9624924ac3e03ff0ab2d5e3616955#file-nvidia-partching-hyprland-ubuntu-md)
