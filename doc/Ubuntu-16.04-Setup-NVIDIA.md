Ubuntu 16.04 Setup Guide (NVIDIA)
=========================

This guide is intended to walk you through the requisite steps for enabling an Ubuntu 16.04 host to run NVIDIA CUDA applications, i.e. crypto-currency mining software, from within a Docker container.

### 1. Prepare System

```bash
#!/bin/bash

# REQUIRED: Upgrade the system

sudo apt-get update
sudo apt-get -y upgrade
```

After upgrading, please reboot the system and connect your GPU devices if you have not already. When reboot completes, you will be able to validate that each device is connected to the PCI bus(es) using the `lspci` utility, e.g.

```bash
#!/bin/bash

# EXAMPLE: List VGA PCI devices

user@host~$ lspci | grep VGA
01:00.0 VGA compatible controller: NVIDIA Corporation Device 1b81 (rev a1)
03:00.0 VGA compatible controller: NVIDIA Corporation Device 1b81 (rev a1)
05:00.0 VGA compatible controller: NVIDIA Corporation Device 1b81 (rev a1)
06:00.0 VGA compatible controller: NVIDIA Corporation Device 1b81 (rev a1)
07:00.0 VGA compatible controller: NVIDIA Corporation Device 1b81 (rev a1)
08:00.0 VGA compatible controller: NVIDIA Corporation Device 1b81 (rev a1)
```

If you do not see all of your GPU devices, stop here and troubleshoot your hardware installation. If you do see everything, please proceed.

If you initially installed Ubuntu without having selected `standard-system-utilities` at the Software Selection step, you will need to install `apt-transport-https` before proceeding.

```bash
#!/bin/bash

# OPTIONAL: Install omitted packages
# This is only required if you omitted `standard-system-utilities` during installation

sudo apt-get install -y --no-install-recommends apt-transport-https
```

Lastly, you may need to disable the `PCI_MSI` and/or `PCIAER` kernel boot parameters if you see a stream of error logs during boot from the `pcieport` kernel driver. This seems to be a common issue, but I do not understand it very well. Instead, please refer to the web for further explanation: [here](https://askubuntu.com/questions/771899/pcie-bus-error-severity-corrected), [here](https://unix.stackexchange.com/questions/327730/what-causes-this-pcieport-00000003-0-pcie-bus-error-aer-bad-tlp), and [here](https://devtalk.nvidia.com/default/topic/1022396/pcie-bus-error-severity-corrected-after-booting-into-ubuntu-16-04-2-lts-/?offset=8)

```bash
#!/bin/bash

# OPTIONAL: Disable PCI_MSI and PCIAER
# See https://askubuntu.com/questions/771899/pcie-bus-error-severity-corrected for details

sudo cp /etc/default/grub /etc/default/grub.original
sudo sed -i 's/GRUB_CMDLINE_LINUX_DEFAULT=""/GRUB_CMDLINE_LINUX_DEFAULT="pci=nomsi,noaer"/g' /etc/default/grub
sudo update-grub
```

### 2. Install NVIDIA CUDA drivers

```bash
#!/bin/bash

# REQUIRED: Install NVIDIA CUDA drivers
# Additional information can be found at http://developer.download.nvidia.com/compute/cuda/9.0/Prod/docs/sidebar/CUDA_Installation_Guide_Linux.pdf

curl -O http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_9.0.176-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1604-9-0-local_9.0.176-1_amd64.deb
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub
sudo apt-get update
sudo apt-get install -y --no-install-recommends cuda
```

After installing the CUDA drivers, if you do not wish to boot into a graphical user interface, you will need to disable the `lightdm` service.

```bash
#!/bin/bash

# OPTIONAL: Disable `lightdm` service

sudo systemctl disable lightdm.service
```

Although not required, I suggest disabling the `snd_hda_intel` kernel module unless you have a legitimate need for the audio driver.

```bash
#!/bin/bash

# OPTIONAL: disable `snd_hda_intel` kernel module
# Do not do this if you need audio!

echo -e "blacklist snd_hda_intel" \
    | sudo tee /etc/modprobe.d/blacklist-snd_hda_intel.conf \
    > /dev/null

sudo update-initramfs -u
```

If you choose to install Cuda via [runfile](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#runfile) you will also need to disable Nouveau.

```bash
#!/bin/bash

# OPTIONAL: disable `nouveau` kernel module
# This is only required if you install Cuda via runfile

echo -e "blacklist nouveau\noptions nouveau modeset=0" \
    | sudo tee /etc/modprobe.d/blacklist-nouveau.conf \
    > /dev/null

sudo update-initramfs -u
```

Once completing NVIDIA CUDA driver installation, you will need to reboot your system. Once the system comes back up, you must verify all VGA PCI devices loaded with the correct driver, i.e. `nvidia`. Do not proceed unless all devices are using the correct driver.

```bash
#!/bin/bash

# EXAMPLE: verify VGA PCI devices using `nvidia` driver

user@host:~$ lspci -k | grep -A2 VGA
01:00.0 VGA compatible controller: NVIDIA Corporation Device 1b81 (rev a1)
	Subsystem: eVga.com. Corp. Device 5173
	Kernel driver in use: nvidia
--
03:00.0 VGA compatible controller: NVIDIA Corporation Device 1b81 (rev a1)
	Subsystem: eVga.com. Corp. Device 5173
	Kernel driver in use: nvidia
--
05:00.0 VGA compatible controller: NVIDIA Corporation Device 1b81 (rev a1)
	Subsystem: eVga.com. Corp. Device 5173
	Kernel driver in use: nvidia
--
06:00.0 VGA compatible controller: NVIDIA Corporation Device 1b81 (rev a1)
	Subsystem: eVga.com. Corp. Device 5173
	Kernel driver in use: nvidia
--
07:00.0 VGA compatible controller: NVIDIA Corporation Device 1b81 (rev a1)
	Subsystem: eVga.com. Corp. Device 5173
	Kernel driver in use: nvidia
--
08:00.0 VGA compatible controller: NVIDIA Corporation Device 1b81 (rev a1)
	Subsystem: eVga.com. Corp. Device 5173
	Kernel driver in use: nvidia
```

### 3. Install Docker

```bash
#!/bin/bash

# REQUIRED: Install Docker Community Edition
# Additional information can be found at https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/

curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
    | sudo apt-key add -

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update
sudo apt-get -y --no-install-recommends install docker-ce
```

### 4. Install NVIDIA-Docker

```bash
#!/bin/bash

# REQUIRED: Install `nvidia-docker2`
# Additional information can be found at https://github.com/NVIDIA/nvidia-docker

curl -fsSL https://nvidia.github.io/nvidia-docker/gpgkey \
    | sudo apt-key add -

curl -fsSL https://nvidia.github.io/nvidia-docker/ubuntu16.04/amd64/nvidia-docker.list \
    | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update
sudo apt-get install -y --no-install-recommends nvidia-docker2
sudo pkill -SIGHUP dockerd
```

Enable the `nvidia-persistenced` daemon so that any configuration updates made via `nvidia-smi` are persisted.

```bash
#!/bin/bash

# REQUIRED: Enable `nvidia-persistenced`
# Additional information can be found at http://docs.nvidia.com/deploy/driver-persistence/index.html

sudo useradd nvidia
sudo mkdir /var/run/nvidia-persistenced
sudo chown -R nvidia /var/run/nvidia-persistenced
sudo nvidia-persistenced --user nvidia
```

### 5. Confirmation

At this point, you should be able to run containerized CUDA applications by passing the `--runtime=nvidia` flag to `docker` `run`. To confirm everything is working, invoke `nvidia-smi` from within a container and verify all GPU devices are listed, e.g.

```bash
#!/bin/bash

# EXAMPLE: Confirm NVIDIA Docker installation successful

auger@auger2:~$ sudo docker run --runtime=nvidia --rm nvidia/cuda nvidia-smi
Sun Nov 26 13:04:30 2017
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 384.90                 Driver Version: 384.90                    |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce GTX 1070    On   | 00000000:01:00.0 Off |                  N/A |
| 30%   61C    P2    94W /  95W |   2392MiB /  8112MiB |    100%      Default |
+-------------------------------+----------------------+----------------------+
|   1  GeForce GTX 1070    On   | 00000000:03:00.0 Off |                  N/A |
| 30%   61C    P2    94W /  95W |   2392MiB /  8114MiB |    100%      Default |
+-------------------------------+----------------------+----------------------+
|   2  GeForce GTX 1070    On   | 00000000:05:00.0 Off |                  N/A |
| 33%   63C    P2    95W /  95W |   2392MiB /  8114MiB |    100%      Default |
+-------------------------------+----------------------+----------------------+
|   3  GeForce GTX 1070    On   | 00000000:06:00.0 Off |                  N/A |
| 33%   63C    P2    95W /  95W |   2392MiB /  8114MiB |    100%      Default |
+-------------------------------+----------------------+----------------------+
|   4  GeForce GTX 1070    On   | 00000000:07:00.0 Off |                  N/A |
| 27%   60C    P2    95W /  95W |   2392MiB /  8114MiB |    100%      Default |
+-------------------------------+----------------------+----------------------+
|   5  GeForce GTX 1070    On   | 00000000:08:00.0 Off |                  N/A |
| 20%   58C    P2    93W /  95W |   2392MiB /  8114MiB |    100%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
+-----------------------------------------------------------------------------+
```

Congratulations! You are ready to start mining!

## References

1. NVIDIA CUDA Installation Guide. http://developer.download.nvidia.com/compute/cuda/9.0/Prod/docs/sidebar/CUDA_Installation_Guide_Linux.pdf
2. Docker CE for Ubuntu. https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
3. NVIDIA-Docker Wiki. https://github.com/NVIDIA/nvidia-docker/wiki
4. NVIDIA Driver Persistence. http://docs.nvidia.com/deploy/driver-persistence/index.html
