# Things to do after installing Ubuntu 24.04 LTS

## Install updates:
```
sudo apt update
sudo apt upgrade
```

## Firmware
* If your system supports firmware update delivery through lvfs, update your device firmware by:
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
* `clinfo | grep "Device Name"`
* You should see the Intel graphics product device names listed. If they do not appear, ensure you have permissions to access /dev/dri/renderD*
```
sudo gpasswd -a ${USER} render
newgrp render
```

## NVIDIA Drivers and Cuda
```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-8
```

## Install and Enable Intel Thermal Daemon (Thermald) - https://github.com/intel/thermal_daemon
```
sudo apt install thermald
sudo systemctl enable thermald.service
sudo systemctl start thermald.service
sudo systemctl status thermald.service
```

## Media Codecs
* Install these to get proper multimedia playback.
```
sudo apt install ubuntu-restricted-extras
```

### H/W Video Decoding with VA-API 
```
sudo apt install vainfo libva ffmpeg
```
* VAAPI driver for Intel G45 & HD Graphics family
```
sudo apt install i965-va-driver
```
* VAAPI driver for the Intel GEN8+ Graphics family
```
sudo apt install intel-media-va-driver intel-media-va-driver-non-free
```
* For NVIDIA nouveau and AMD Chipset:
```
sudo apt install mesa-va-drivers mesa-vdpau-drivers vdpau-driver-all
```

