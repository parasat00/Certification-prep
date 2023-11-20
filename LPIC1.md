# LPIC1: A simple personal cheatsheet

_**Note:**_ This document is not completed.  
This is my personal **lpic1** cheatsheet.

## System Architecture

### Determine and Configure Hardware Settings 
Weight: 2

``` bash
# Delete, F2 or F12 to enter configuration utility (BIOS, UEFI)

sudo lspci                                 # will show all of the devices connected to the Peripheral Component Interconnect (PCI) bus
sudo lspci -s [hardware address] -v        # more detailed information (with kernel info) about a specific (-s) device. (-v or verbose)
sudo lspci -s [hardware address] -k        # kernel information the specific (-s) device uses. (-k stands for kernel)

sudo lsusb                                 # shows devices connected through a USB.
sudo lsusb -v -d [device id]               # more detailed information about a specific (-d) device. (-v or verbose)
sudo lsusb -t                              # shows USB devices in hierarchial/tree format.
sudo lsusb -s [bus]:[dev number]           # more detailed information about a specific (-s) device.

sudo lsmod                                 # shows a list of kernel modules available on the system
sudo lsmod | fgrep -i [module name]        # search for a module by name in the list
sudo modprobe -r [module name]             # unload a kernel module that is currently in use (-r stands for remove)

sudo modinfo [module name]                 # shows detailed info(with parameters) about a specific module
sudo modinfo -p [module name]              # shows parameters of specific module (-p stands for parameters)

sudo vi /etc/modprobe.d/[module name].conf      # to make persistent changes to the parameters of the module (create file if it doesnt exist)
sudo vi /etc/modprobe.d/blacklist.conf          # to block kernel module from loading add a new line like this: blacklist [module name]

# /proc and /sys are special mount points that exist in RAM (only exist when system is running), they are used by the kernel to store information on running processes.

# /proc/cpuinfo                            Contains detailed information about the CPU(s)
# /proc/interrupts                         A list of numbers of the interrupts per IO device for each CPU.
# /proc/ioports                            Lists currently registered Input/Output port regions in use.
# /proc/dma                                Lists the registered DMA (direct memory access) channels in use.

# Every file inside /dev is associated with a system device, particularly storage devices.

# Removable devices are handled by the udev subsystem, which creates the corresponding devices in /dev.
# udev is responsible for the identification and configuration of the devices already present during machine power-up (coldplug detection)
# udev is also responsible for the identification and configuration of the devices while the system is running (hotplug detection)
# As new devices are detected, udev searches for a matching rule in the pre-defined rules stored in the directory /etc/udev/rules.d/

# IDE, SSD and USB block devices prefixed by sd. F.e. /dev/sda1, /dev/sda2.
# SD cards prefixed like:  /dev/mmcblk0p1, /dev/mmcblk0p2, /dev/mmcblk1p1, /dev/mmcblk1p2.
# NVme cards prefixed like: /dev/nvme0n1p1 and /dev/nvme0n1p2.
```