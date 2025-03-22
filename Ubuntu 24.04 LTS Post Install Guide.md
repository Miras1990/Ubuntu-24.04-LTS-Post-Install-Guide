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

## Reduce the Systemd timeout for services
```
sudo nano /etc/systemd/system.conf
DefaultTimeoutStartSec=15s
DefaultTimeoutStopSec=15s
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
