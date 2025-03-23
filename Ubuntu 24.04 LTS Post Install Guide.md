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

## Intel(R) Graphics Compute Runtime for oneAPI Level Zero and OpenCL(TM) Driver
* Install the Intel graphics GPG public key:
```
wget -qO - https://repositories.intel.com/gpu/intel-graphics.key | \
  sudo gpg --yes --dearmor --output /usr/share/keyrings/intel-graphics.gpg
```
* Configure the repositories.intel.com package repository:
```
echo "deb [arch=amd64,i386 signed-by=/usr/share/keyrings/intel-graphics.gpg] https://repositories.intel.com/gpu/ubuntu noble unified" | \
  sudo tee /etc/apt/sources.list.d/intel-gpu-noble.list
```
* Update the package repository metadata and Install the compute-related packages:
```
sudo apt install libze-intel-gpu1 libze1 intel-opencl-icd clinfo intel-gsc libze-dev intel-ocloc
```
* If you wish to enable hardware ray tracing support, install intel-level-zero-gpu-raytracing additionally:
```
sudo apt install intel-level-zero-gpu-raytracing
```
* Verifying installation
```
clinfo | grep "Device Name"
```
* You should see the Intel graphics product device names listed. If they do not appear, ensure you have permissions to access /dev/dri/renderD*
```
sudo gpasswd -a ${USER} render
newgrp render
```
* [More info](https://dgpu-docs.intel.com/driver/client/overview.html)

## NVIDIA Drivers and [Cuda Toolkit](https://developer.nvidia.com/cuda-downloads?target_os=Linux)
```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-8
```

## Install and Enable Intel Thermal Daemon [Thermald](https://github.com/intel/thermal_daemon)
```
sudo apt install thermald
sudo systemctl enable thermald.service
sudo systemctl start thermald.service
sudo systemctl status thermald.service
```

## Media Codecs
* Install these to get proper multimedia playback.
```
sudo apt install ubuntu-restricted-extras libavcodec-extra
```

## H/W Video Decoding with VA-API 
```
sudo apt install vainfo libva ffmpeg`
```
* VAAPI driver for Intel G45 & HD Graphics family
```
sudo apt install i965-va-driver`
```
* VAAPI driver for the Intel GEN8+ Graphics family
```
sudo apt install intel-media-va-driver intel-media-va-driver-non-free`
```
* For NVIDIA nouveau and AMD Chipset:
```
sudo apt install mesa-va-drivers mesa-vdpau-drivers vdpau-driver-all
```

## Microsoft Fonts:
```
sudo apt install -y ttf-mscorefonts-installer
```

## [Visual Studio Code on Linux](https://code.visualstudio.com/docs/setup/linux)
* Manually install the apt repository
```
sudo apt-get install wget gpg
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" |sudo tee /etc/apt/sources.list.d/vscode.list > /dev/null
rm -f packages.microsoft.gpg
```
* Then update the package cache and install the package using:
```
sudo apt install apt-transport-https
sudo apt update
sudo apt install code
```
* VS Code is officially distributed as a Snap package in the [Snap Store](https://snapcraft.io/store)
* You can install it by running:
```
sudo snap install --classic code
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
media.ffmpeg.vaapi.enabled  true
media.navigator.mediadatadecoder_vpx_enabled  true
layers.acceleration.force-enabled true
gfx.webrender.enabled true
gfx.webrender.all true
gfx.x11-egl.force-enabled true
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
sudo apt install clinfo ricks-amdgpu-utils`
```

### Disable `NetworkManager-wait-online.service`
* Disabling it can decrease the boot time by at least ~15s-20s:
```
sudo systemctl disable NetworkManager-wait-online.service
```

### Better Linux Disk Caching & Performance with vm.dirty_ratio & vm.dirty_background_ratio
```
vm.dirty_background_ratio = 5
vm.dirty_background_bytes = 0
vm.dirty_ratio = 10
vm.dirty_bytes = 0
vm.dirty_writeback_centisecs = 500
vm.dirty_expire_centisecs = 3000
```

### Gnome Extensions and Tweaks
```
sudo apt install gnome-tweaks dconf-editor gnome-browser-connector gnome-shell-extension-manager
```

### Quickly Enable Minimise on Click in Ubuntu Dock
```
gsettings set org.gnome.shell.extensions.dash-to-dock click-action 'minimize'
```

### GNOME volume step adjustment
```
gsettings set org.gnome.settings-daemon.plugins.media-keys volume-step 1
```

