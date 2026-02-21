# Things to do after installing Ubuntu 24.04 LTS

## Install updates:
```
sudo apt update
sudo apt upgrade
```

## Firmware
* If your system supports firmware update delivery through [lvfs](https://fwupd.org/), update your device firmware by:
```
sudo fwupdmgr refresh --force
sudo fwupdmgr get-devices # Lists devices with available updates.
sudo fwupdmgr get-updates # Fetches list of available updates.
sudo fwupdmgr update
```

## [Intel(R) Graphics Compute Runtime for oneAPI Level Zero and OpenCL(TM) Driver](https://dgpu-docs.intel.com/driver/client/overview.html)
Refresh the local package index and install the package for managing software repositories.
```
sudo apt update
sudo apt install software-properties-common
```
Add the intel-graphics PPA.
```
sudo add-apt-repository -y ppa:kobuk-team/intel-graphics
```
Install the compute-related packages.
```
sudo apt install libze-intel-gpu1 libze1 intel-metrics-discovery intel-opencl-icd clinfo intel-gsc
```
Install the media-related packages.
```
sudo apt install intel-media-va-driver-non-free libmfx-gen1 libvpl2 libvpl-tools libva-glx2 va-driver-all vainfo
```
The commands listed above install all the essential packages needed for most users, aiming to minimize the installation of unnecessary packages. However, if you plan to use PyTorch, install libze-dev and intel-ocloc additionally:
```
sudo apt install libze-dev intel-ocloc
```
If you wish to enable hardware ray tracing support, install libze-intel-gpu-raytracing additionally:
```
sudo apt install libze-intel-gpu-raytracing
```
To verify that the kernel and compute drivers are installed and functional, run clinfo:
```
clinfo | grep "Device Name"
```
You should see the Intel graphics product device names listed. If they do not appear, ensure you have permissions to access /dev/dri/renderD*. This typically requires your user to be in the render group:
```
sudo gpasswd -a ${USER} render
newgrp render
```

## [NVIDIA Cuda Toolkit](https://developer.nvidia.com/cuda-downloads?target_os=Linux)
```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-8
```

## NVIDIA Drivers from ppa
```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
```

## [Install and Enable Intel Thermal Daemon](https://github.com/intel/thermal_daemon) - Intel Platform
```
sudo apt install thermald
sudo systemctl enable thermald.service
sudo systemctl start thermald.service
sudo systemctl status thermald.service
```

## Media Codecs
* Install these to get proper multimedia playback:
```
sudo apt install ubuntu-restricted-extras libavcodec-extra
```

## H/W Video Decoding with VA-API 
```
sudo apt install vainfo libva2 ffmpeg
```
* VAAPI driver for Intel G45 & HD Graphics family:
```
sudo apt install i965-va-driver
```

* For NVIDIA nouveau and AMD Chipset:
```
sudo apt install mesa-va-drivers mesa-vdpau-drivers vdpau-driver-all
```

## [DaVinci Resolve](https://www.blackmagicdesign.com/pl/products/davinciresolve)

* The program works and detects Intel's integrated graphics!
* Installation
```
sudo apt install libfuse2t64 libapr1t64 libaprutil1t64 libasound2t64 libglib2.0-0t64 libxcb-composite0 libxcb-cursor0 libxcb-xinerama0 libxcb-xinput0
unzip DaVinci_Resolve_Studio_*_Linux.zip
chmod +x ./DaVinci_Resolve_Studio_*_Linux.run
sudo SKIP_PACKAGE_CHECK=1 ./DaVinci_Resolve_Studio_*_Linux.run -i
```
* Repair (/opt/resolve/bin/resolve: symbol lookup error: /lib/x86_64-linux-gnu/libpango-1.0.so.0: undefined symbol: g_once_init_leave_pointer):
```
sudo mkdir /opt/resolve/libs/unneeded
sudo mv /opt/resolve/libs/libgio* /opt/resolve/libs/unneeded/
sudo mv /opt/resolve/libs/libglib* /opt/resolve/libs/unneeded/
sudo mv /opt/resolve/libs/libgmodule* /opt/resolve/libs/unneeded/
```
[Source](https://www.dedoimedo.com/computers/davinci-resolve-ubuntu-24-04.html)

## [DeepCool Digital for Linux](https://github.com/Nortank12/deepcool-digital-linux)

* Automatic Start with Systemd 
```
sudo cp ./deepcool-digital-linux /usr/sbin/
sudo nano /etc/systemd/system/deepcool-digital.service
```
* Insert the following:
```
[Unit]
Description=DeepCool Digital

[Service]
ExecStart=/usr/sbin/deepcool-digital-linux
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
```
* Enable the service:
```
sudo systemctl enable deepcool-digital
```
## [LogiOps](https://github.com/PixlOne/logiops)

* Dependencies:
```
sudo apt install build-essential cmake pkg-config libevdev-dev libudev-dev libconfig++-dev libglib2.0-dev git
```
* Building:
```
git clone https://github.com/PixlOne/logiops.git
cd logiops/
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
```
* Installation:
```
sudo make install
```
Download [logid.example.cfg](https://github.com/PixlOne/logiops/blob/main/logid.example.cfg) file and copy its contents to "/etc/logid.cfg"
* Enable service:
```
sudo systemctl enable --now logid
```

## Configuring the system and the GNOME graphical environment

### Set Hostname
```
hostnamectl set-hostname YOUR_HOSTNAME
```

### Set UTC Time
* Fixing Windows Showing Wrong Time in a Dual Boot Setup With Linux:
```
sudo timedatectl set-local-rtc 1
```

### Enable VAAPI in Firefox "about:config"
```
media.navigator.mediadatadecoder_vpx_enabled  true
layers.acceleration.force-enabled true
gfx.webrender.all true
```

### Reduce the Systemd timeout for services
```
sudo nano /etc/systemd/system.conf
DefaultTimeoutStartSec=15s
DefaultTimeoutStopSec=15s
```

### Enable Support for ntfs-3g and exfat:
```
sudo apt install ntfs-3g exfat-fuse
```

### Enable Trim Support
```
sudo systemctl enable fstrim.timer
sudo systemctl start fstrim.timer
sudo fstrim -v /
```

### Install lm-sensors and detect all sensors
```
Add module nct6775:
sudo nano /etc/modules-load.d/modules.conf
sudo apt install lm-sensors
sudo sensors-detect
```

### Enable firewall
```
sudo apt install gufw
sudo ufw enable
```

### [Ricks-Lab GPU Utilities](https://github.com/Ricks-Lab/gpu-utils) - monitoring AMD GPU
```
sudo apt install clinfo ricks-amdgpu-utils
```

### Disable `NetworkManager-wait-online.service`
* Disabling it can decrease the boot time by at least ~15s-20s:
```
sudo systemctl disable NetworkManager-wait-online.service
```

### Better Linux Disk Caching & Performance with vm.dirty_ratio & vm.dirty_background_ratio & Sharply reduce swap inclination
```
vm.swappiness = 1
vm.vfs_cache_pressure = 50
vm.dirty_background_ratio = 5
vm.dirty_background_bytes = 0
vm.dirty_ratio = 10
vm.dirty_bytes = 0
vm.dirty_writeback_centisecs = 500
vm.dirty_expire_centisecs = 3000
```

### Gnome Extensions and Tweaks
```
sudo apt install gnome-tweaks dconf-editor gnome-browser-connector
```

### Quickly Enable Minimise on Click in Ubuntu Dock
```
gsettings set org.gnome.shell.extensions.dash-to-dock click-action 'minimize'
```

### GNOME volume step adjustment
```
gsettings set org.gnome.settings-daemon.plugins.media-keys volume-step 1
```

### Firefox Theme
* https://github.com/rafaelmardojai/firefox-gnome-theme

### Gnome Extensions
* [Quick Settings Tweaker](https://github.com/qwreey75/quick-settings-tweaks)
* [User Themes](https://extensions.gnome.org/extension/19/user-themes/)
* [Vitals](https://extensions.gnome.org/extension/1460/vitals/)

## Snaps
```
sudo snap install --classic code
sudo snap install --classic blender
sudo snap install discord
sudo snap install spotify

```

## Apps
* unzip
* p7zip
* p7zip-full
* unrar
* Blender
* Discord 
* Spotify
* GIMP
* vlc
* darktable
* mc
* bpytop
* inxi
* libreoffice
* audacity
