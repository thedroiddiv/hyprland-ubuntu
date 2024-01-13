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

### Installing dependencies
```bash
sudo apt-get install -y nala
sudo nala install -y meson wget build-essential ninja-build cmake-extras cmake gettext gettext-base fontconfig libfontconfig-dev libffi-dev libxml2-dev libdrm-dev libxkbcommon-x11-dev libxkbregistry-dev libxkbcommon-dev libpixman-1-dev libudev-dev libseat-dev seatd libxcb-dri3-dev libvulkan-dev libvulkan-volk-dev  vulkan-validationlayers-dev libvkfft-dev libgulkan-dev libegl-dev libgles2 libegl1-mesa-dev glslang-tools libinput-bin libinput-dev libxcb-composite0-dev libavutil-dev libavcodec-dev libavformat-dev libxcb-ewmh2 libxcb-ewmh-dev libxcb-present-dev libxcb-icccm4-dev libxcb-render-util0-dev libxcb-res0-dev libxcb-xinput-dev libpango1.0-dev xdg-desktop-portal-wlr hwdata libwlroots-dev libliftoff-dev libinput-tools
```

### Clone Sources
#### Hyprland release
```
wget https://github.com/hyprwm/Hyprland/releases/download/v0.34.0/source-v0.34.0.tar.gz
tar -xvf source-v0.34.0.tar.gz
```
#### Libraries that needs to be built and installed manually
```bash
wget https://gitlab.freedesktop.org/wayland/wayland-protocols/-/releases/1.32/downloads/wayland-protocols-1.32.tar.xz
tar -xvJf wayland-protocols-1.32.tar.xz

wget https://gitlab.freedesktop.org/wayland/wayland/-/releases/1.22.0/downloads/wayland-1.22.0.tar.xz
tar -xvJf wayland-1.22.0.tar.xz

wget https://gitlab.freedesktop.org/emersion/libdisplay-info/-/releases/0.1.1/downloads/libdisplay-info-0.1.1.tar.xz
tar -xvJf libdisplay-info-0.1.1.tar.xz

wget https://dri.freedesktop.org/libdrm/libdrm-2.4.119.tar.xz
tar -xvf libdrm-2.4.119.tar.xz

wget https://github.com/marzer/tomlplusplus/archive/refs/tags/v3.4.0.tar.gz
tar -xvf v3.4.0.tar.gz
```

### Build and install
#### Building Wayland
```bash
cd wayland-1.22.0
mkdir build &&
cd    build &&

meson setup ..            \
      --prefix=/usr       \
      --buildtype=release \
      -Ddocumentation=false &&
ninja
sudo ninja install

cd ../..
```

#### Building Wayland-protocols
```bash
cd wayland-protocols-1.32

mkdir build &&
cd    build &&

meson setup --prefix=/usr --buildtype=release &&
ninja

sudo ninja install

cd ../..
```

#### Building libdisplay-info
```bash
cd libdisplay-info-0.1.1/

mkdir build &&
cd    build &&

meson setup --prefix=/usr --buildtype=release &&
ninja

sudo ninja install

cd ../..
```

#### Building libdrm
```bash
cd libdrm-2.4.119/

mkdir build &&
cd    build &&

meson setup --prefix=/usr \
            --buildtype=release   \
            -Dudev=true           \
            -Dvalgrind=disabled   \
            ..                    &&
ninja
sudo ninja install
```

#### Building tomlplusplus
```bash
cd tomlplusplus-3.4.0/

mkdir build &&
cd    build &&

meson setup --prefix=/usr --buildtype=release

ninja
sudo ninja install

cd ../..
```

#### Build and install Hyprland
```bash
chmod a+rw hyprland-source
cd hyprland-source/
```

Apply NVIDIA patch (optional, needed only when building for NVIDIA)
```bash
sed 's/glFlush();/glFinish();/g' -i subprojects/wlroots/render/gles2/renderer.c
```
Build and install
```bash
meson setup build -Dbuildtype=release
ninja -C build
cd build
sudo ninja install
```

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
