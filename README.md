# Implementation of GPU on Virtual Machine
When the VM is created, a number of options need to be added to the VM, which are explained in the link below.
[vmware](https://blogs.vmware.com/apps/2018/09/using-gpus-with-virtual-machines-on-vsphere-part-2-vmdirectpath-i-o.html)

## Essential options:
 * pciPassthru.use64bitMMIO

 * pciPassthru.64bitMMIOSizeGB

For tesla type GPUs like a100, and v100, it is necessary that the os to be installed, and its boot is of EFI type.
The kernel version needs to be above 5.10. For kernels above 5.15, the driver version should be above 500.

With the following command, you can list the GPUs assigned to the machine.

```bash
lspci | grep -i nvidia
```

## Install Driver
There are 3 ways to install the driver:

- run file

- ubuntu apt repository

- nvidia apt repository



Nvidia has recommended using the ubuntu apt repository in its forums.

### run file

You can use the link below to download the run file.

[run file](https://www.nvidia.com/Download/Find.aspx?lang=en-us)

- modprobe blacklist
```bash
 sudo bash -c "echo -e blacklist nouveau\noptions nouveau modeset=0 > /etc/modprobe.d/disable-nouveau.conf"
 sudo bash -c "echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf"
 sudo bash -c "echo options nouveau modeset=0 >> /etc/modprobe.d/blacklist-nvidia-nouveau.conf"
 sudo update-initramfs -u
 sudo reboot
```
- Install necessary packages
```bash
apt-get install gcc make
```

To install the driver, it is enough to give the downloaded file permission to run.
```bash
chmod +x ./NVIDIA-Linux-....run 
./NVIDIA-Linux-....run
```
For install Open-Source version:
```bash
sudo bash -c "echo -e options nvidia NVreg_OpenRmEnableUnsupportedGpus=1 >> /etc/modprobe.d/nvidia-open-source.conf"
./NVIDIA-Linux-....run -m=kernel-open
```

With the following commands, you can see the version of the installed driver and the version of cuda.
```bash
nvidia-smi
cat /proc/driver/nvidia/version
```

### ubuntu apt repository:

There is no need to change the repository to install the driver.

The name of the package is Nvidia-driver-{{VERSION}}. The driver version is in the package name.

When the ubuntu repository is used for installation, the latest version of the driver must be installed. Step versions often have problems.

To install the open source driver, the package name will be Nvidia-driver-{{VERSION}}-open.
ubuntu apt repository:
There is no need to change the repository to install the driver.

The name of the package is Nvidia-driver-{{VERSION}}. The driver version is in the package name.

When the ubuntu repository is used for installation, the latest version of the driver must be installed. Step versions often have problems.

To install the open source driver, the package name will be Nvidia-driver-{{VERSION}}-open.

### Nvidia apt repository:

Use the link below to install the driver
[Link](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html#ubuntu-lts)

After adding the repository related to Nvidia, you can use both cuda-drivers package and Nvidia-driver-{{VERSION}}:{{NVIDIA-VERSION}}.

If the Nvidia-driver-{{VERSION}} package is used, it is necessary to mention the version of the package in the repository during installation so that ubuntu packages are not used.

An error related to the version of dependent packages may be seen when using nvidia repository. To solve this problem, all driver dependencies must be installed with the same version from Nvidia repository. Then the driver can be installed.

## Uninstall driver:

If a problem occurs while installing the driver, the previous driver must be deleted and then the new driver must be installed.

### apt repository:
With the following command, you can list all the installed nvidia packages.

```bash
apt list --installed | grep -i nvidia
```
To remove the driver, all the packages listed with the command apt purge PACKAGE-NAME must be removed

### run file:
The following format is used to remove the driver.
```bash
./NVIDIA-Linux-....run -uninstall
```

## Contributing

To install the driver with a version higher than 515, if the run file is used, it is usually necessary to use the open-source version of the driver.

