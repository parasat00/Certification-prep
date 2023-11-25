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

### Boot the system
Weight: 3

``` bash
# BIOS/UEFI >>> Bootloader >>> Kernel >>> Init

# The most popular bootloader for Linux in the x86 architecture is GRUB (Grand Unified Bootloader)

sudo vi /etc/default/grub                   # To edit/add/delete kernel parameters
grub-mkconfig -o /boot/grub/grub.cfg        # To make changes persistent
        # or use
update-grub                                 # Does the same

sudo cat /proc/cmdline                      # To read the kernel parameters used for loading the current session

# The memory space where the kernel stores its messages, including the boot messages, is called the kernel ring buffer.

dmesg                                       # Displays the current messages in the kernel ring buffer
dmesg -H                                    # Same with dmesg command, but with pager (-H or --human can be used)
dmesg --clear                               # Removes all messages from kernel ring buffer

journalctl                                  # Shows the initialization messages
journalctl --list-boots                     # Shows a list of boot numbers relative to the current boot (0 -current, -1 -previous, -2..)
journalctl -b 0                             # Messages for the current boot will be shown

# /var/log/  initialization and system logs
# /var/log/journal/ is the default location for systemd log messages.

journalctl -D /var/log/other_directory      # Will display logs from other_directory (-D or --directory can be used)

# Initialization and other messages issued by the operating system are stored in files inside the directory /var/log/.

```

### Change runlevels / boot targets and shutdown or reboot system
Weight: 3

SysVinit runlevels:
* Runlevel 0 || System shutdown.
* Runlevel 1, s or single || Single user mode, without network and other non-essential capabilities (maintenance mode).
* Runlevel 2, 3 or 4 || Multi-user mode. Users can log in by console or network. Runlevels 2 and 4 are not often used.
* Runlevel 5 || Multi-user mode. It is equivalent to 3, plus the graphical mode login.
* Runlevel 6 || System restart.

The program responsible for managing runlevels and associated daemons/resources is /sbin/init.

/etc/inittab - defines each run level
/etc/init.d  - Contains script for each runlevel

The default runlevel — the one that will be chosen if no other is given as a kernel parameter.
Defined in /etc/inittab, in the entry id:x:initdefault. This number should never be 0 or 6t
The system will shutdown or restart as soon as it finishes the boot process. 

``` bash
telinit q                                # reloads init, should be executed every time the /etc/inittab file is modified.

# First letter of the link filename in the runlevel’s directory indicates if the service should be started (S)
# or terminated (K) for the corresponding runlevel.

runlevel                                 # shows two values, the previous runlevel and the current runlevel

init 1                                   # alternates runlevel 1 without the need to reboot
telinit 1                                # alternates runlevel 1 without the need to reboot (s or S can also be used)
```

systemd
Currently, the most widely used set of tools to manage system resources and services, which are referred to as units by systemd. 
The main command for controlling systemd units is systemctl. Command systemctl is used to execute all tasks regarding unit activation, deactivation, execution, interruption, monitoring
``` bash
# If no other units with the same name exist in the system, then the suffix after the dot can be dropped
systemctl start unit.service                           # Starts unit.
systemctl stop unit.service                            # Stops unit.
systemctl restart unit.service                         # Restarts unit.
systemctl status unit.service                          # Shows the state of the unit, including if it is running or not.
systemctl is-active unit.service                       # Shows active if the unit is running or inactive otherwise.
systemctl enable unit.service                          # Enables unit, that is, unit will load during system initialization.
systemctl disable unit.service                         # Unit will not start with the system.
systemctl is-enabled unit.service                      # Verifies if a unit starts with the system. The answer is stored in the variable $?

systemctl get-default                                            # system’s default boot target/Runlevel
systemctl set-default [multi-user.target or graphical.target]    # changes system’s default boot target
# The default target should never point to shutdown.target, as it corresponds to the runlevel 0 (shutdown)

sudo systemctl list-unit-files                         # lists all available units and shows if they are enabled
sudo systemctl list-unit-files --type=service          # filters the lists so only service units can be seen
sudo systemctl list-units                              # lists active units or units that have been active during the current system session

systemctl suspend                                      # saves the current state of the system into the RAM and cuts the power supply of all devices except the RAM
systemctl hibernate                                    # will copy all memory data to disk, so the current state of the system can be recovered after powering it off
# both commands can only be used when there is no other power manager running in the system, like the acpid daemon
```

Upstart
! Upstart was developed for the Ubuntu Linux distribution to help facilitate parallel startup of processes. Ubuntu has stopped using Upstart since 2015 when it switched from Upstart to systemd.

The initialization scripts used by Upstart are located in the directory /etc/init/.
```bash
initctl list                                           # lists services, their current state and PID(if available)
sudo start tty6                                        # starts the service named tty6
sudo status tty6                                       # checks the status of the service named tty6
sudo stop tty6                                         # stops the service named tty6
```
