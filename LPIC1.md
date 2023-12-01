# LPIC1: A simple personal cheatsheet

_**Note:**_ This document is not completed.  
This is my personal **lpic1** cheatsheet.

## GNU and Unix Commands
### Perform basic file management
Weight: 4

Everything in Linux is a file, so knowing how to manipulate them is very important.
```bash
ls                                 # Lists files and directories
ls -a                              # Lists all files and directories including hidden files
ls -l                              # Lists file or directory permissions, owner, size, modified date, time and name
ls -lh                             # Similar to previous command, -h shows file sizes in a human readable format
ls -R [dir]                        # Lists content of dir with its subdirectories and files

tree [dir]                         # Displays content of dir with its subdirectories and files in tree like manner

touch [file]                       # Creates an empty file(s)

cp [source file] [dir]             # Copies source file inside dir. Both can be specified as absolute or relative paths
cp -r [source] [destination]       # Copies source directory inside destinstion directory
mv [file] [dir]                    # Moves(copies and deletes original) file inside dir
mv [before] [after]                # Can also be used to rename a file
mv -i [before] [after]             # Will ask user's permission before overwrite
mv -f [before] [after]             # Will forcefully overwrite file without user's permission

rm [file]                          # Removes file(s)
rm -i [file]                       # Asks to confirm before deleting file
rm -f [file]                       # Will forcefully deletes file without user's confirmation
rm -r [dir]                        # Deletes directory and everything inside of it

mkdir [dir]                        # Creates directory (or directories) inside user's current directory
mkdir -p [parent]/[child]          # Creates both parent and child directory. Child created inside parent

rmdir dir                          # Removes directory (or directories) ONLY IF DIR IS EMPTY
rmdir -p [parent]/[child]          # Removes both parent and child ONLY IF BOTH ARE EMPTY

# Wildcards are essentially symbols which may be used to substitute for one or more characters.
# Types of Wildcards:
#  * (asterisk)     --which represents zero, one or more occurrences of any character.
#  ? (question mark)  --which represents a single occurrence of any character.
#  [ ] (bracketed characters) --which represents any occurrence of the character(s) enclosed in the square brackets.
#                               For example, the expression [0-9] matches all digits.

# Find Files
find [start path] [option] [expression]    # Finds any file(s) starting from given directory that match expression
      find . -name '*.pdf'                 # Finds pdf files in a current directory

#      -type:
#              f ---> searches among files    f.e.     find . -type f -name "example"
#              d ---> searches among directory      f.e.     find . -type d -name "example"
#              l ---> searches among symbolic links        f.e.     find . -type l -name "example"
#      -name   --->  search based on the given name
#      -iname  --->  search based on the given name case insensitive (myFile=MYFile)
#      -not    --->  returns those results that do not match the test case
#      -maxdepth N   --->  searches the given directory as well as subdirectories N levels deep
#      -mtime        --->  searches based on when the file was modified (days)
#      -size   --->  searches based on file size. F.e.:

        find . -name '*.pdf' -size 100b     # Finds any pdf files with size exactly 100 bytes
        find . -name '*.pdf' -size +100k    # Finds any pdf files larger than 100 killobyes
        find . -name '*.pdf' -size -100M    # Finds any pdf files smaller than 100 megabytes
        find . -size 0b                     # Finds empty files
        find . --empty                      # Finds empty files

#  -exec ---> allows to perform an action on resulting set. F.e.

#    '{}' ---> placeholder for the find match results;   \; ---> terminates exec command

find . -name '*.conf' -exec chmod 644 '{}' \;    # Finds .conf files in the current directory, changes their permissions
find . -type f -exec grep "lpi" '{}' \; -print   # Prints files containing word "lpi" in current directory
find . -name '*.bak' -delete                     # Deletes all files which name end with 'bak'




```

### Work on the command line
Weight: 4

```bash
ls -a                              # Lists all files(hidden included)
pwd                                # Prints the present working directory
touch [file name]                  # Creates file with given name
uname -a                           # Prints the  Linux kernel version, distribution and release
man [command]                      # Accesses help files documenting command usage
mandb                              # Updates the database for apropos
                                   # Otherwise, apropos will not have any results to return.
apropos [keyword]                  # Helps to find command by keyword using its man pages
type [command]                     # Prints type and absolute path of one or more command
which [command]                    # Prints absolute path of a command
history                            # Prints previously used commands
# .bash_history is a hidden file that stores user's previously used command (inside user's home dir)

env                                # Prints current environmental variables
set                                # Prints all environmental variables (local too)
echo [string]                      # Prints string
echo $[variable]                   # Prints a variable

[variable]=[value]                 # Creating a new variable and assigning a value
export [varable]=[value]           # Creates a new variable, variable can be passed to child shells
unset [variable]                   # Deletes a variable
# These environmental variables will not be saved on reboot, to save it use:
echo 'export [variable]=[value]' >> $HOME/.profile      # (or ~/.pam_environment)

bash                               # Opens a new child shell
exit                               # Exits a shell

# Dealing with special characters
#        Special characters:         & ; | * ? " ' [ ] ( ) $ < > { } # / \ ! ~.
touch my big file                  # Creates a three files
touch "my big file"                # Creates one file 'my big file'
touch 'my big file'                # Creates one file 'my big file'
touch my\ big\ file                # Creates one file 'my big file'
# backslash("\") will “escape” the specialness of the character

# Single quotes will preserve the literal value of all characters
# While double quotes will preserve all characters except for $, `, \ and, in certain cases, !
echo "$PATH"                       # Prints Path variable
echo '$PATH'                       # Prints '$PATH'
```
Process text streams using filters
Weight: 2
```bash
cat [file]                         # Prints content
tac [file]                         # Prints lines in reverse order
cat > [file]                       # Rewrites content
cat >> [file]                      # Appends text to content of file

grep [word] [file]                 # Prints only lines that contain word
grep -i [word] [file]              # The same as before but case insensitive
grep -v [word] [file]              # Prints everything but not the lines that contain word

cat [file] | grep [word]           # The same with (grep [word] [file])
#   pipeline (|) -- takes output of one command as an input for second

cut -d [delimeter] -f [number] [file]      # Cuts given field of the content
# F.e. cut -d ':' -f 1 [file] ----> outputs first colon of the file each column separated by :

uniq [file]                        # Does not print the line if the line is the same as the previous
sort [file]                        # Sorts the content

sort [file] | uniq                 # Removes duplicate values from file

diff [file1] [file2]               # Prints differences between two files
diff -c [file1] [file2]            # The same as previous but with more context
diff -y [file1] [file2]            # Prints content of two files line by line, marking different lines
sdiff [file1] [file2]              # The same with the previous

gzip [file]                        # Compresses (reduces size) a [file]
                                   # It actually creates compressed version of the file and then deletes original
zcat [file]                        # Prints content of the compressed file
#bzcat for bzip, zcat for gzip, xzcat for xz compressed files
gunzip [file]                      # Decompresses a file

less [file]                        # Shows content of the file in the pager (useful for big files)
head [file]                        # Prints first 10 lines of file
tail [file]                        # Prints last 10 lines of file
head -n 5 [file]                   # Prints first 5 lines of file
head [file] | nl                   # Prints first 10 lines of file with the number of line
head [file] | wc                   # Prints number of words in the first ten lines of file (wc -- word count)
head [file] | wc -l                # Prints number of lines

# Stream editor sed (previews without changing the file)
sed 's/[first word]/[second word]/g' [file]    # Swaps (s) every occurence of first word with the second word
                                               # g means globally, at every line at every occurence
sed -i 's/[first word]/[second word]/g' [file] # Swaps every first word with second word and saves output to original file
sed -i.backup 's/[first word]/[second word]/g' [file] # Swaps every first word with second word and saves output to file with name
                                                                                              # (original file name + .backup )

sed -n /[word]/p [file]            # Lists only the lines containing the word (-n means dont print unless; p - print)
sed /[word]/d [file]               # Lists every line not containing word (d--delete)

# Ensuring Data Integrity

#  Cryptographic hash function like md5sum, sha256sum and sha512sum are used to calculate checksum values
sha256sum [file]                   # Calculates checksum value of the file
sha256sum [file] > [textfile]      # Calculate checksum and store it inside textfile
sha256sum -c [textfile]            # Verifies the integrity of the file
# If the file is bad or corrupted it will ouput computed checksum did NOT match warning message. Lets test it:
echo "some text" >> [file]         # We changed original file
sha256sum -c [textfile]            # Outputs: ftu.txt: FAILED; sha256sum: WARNING: 1 computed checksum did NOT match

# Octal Dump (od)
od [file]                          # Outputs a content of the file in octal format
od -x [file]                       # Outputs a content of the file in hexadecimal format
od -c [file]                       # Outputs all characters (hidden too \n) in file
od -An -c [file]                   # Outputs all characters of the file without byte offset

split -l [number] -d [file] [prefix]    # Splits a file into several files (original unchanged)
# -l [number] --> each file will contain [number] lines || -d --> tells split to number files like [prefix]00,[prefix]01,etc.

paste -d, [file1] [file2]          # joins files horizontally (parallel merging) separated by ,
# joins files by outputting lines consisting of lines from each file specified, separated by tab as delimiter(by default)
```

## System Architecture

### Determine and Configure Hardware Settings 
Weight: 2

``` bash
# Delete, F2 or F12 to enter configuration utility (BIOS, UEFI)

sudo lsblk                                 # Lists block devices

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
### Shutdown and Restart
```bash
shutdown [option] [time] [message]                     # sends message as a warning and shuts down machine at given time
# time can be divided as hh:mm or +m (wait m minutes before shutdown) or now (+0)

sudo systemctl reboot                                  # restarts the system
sudo systemctl #poweroff                               # turns off the system

sudo wall [text file/message]                          # sends a message to terminal sessions of all logged-in users
```

## Linux Installation and Package Management
### Design hard disk layout

 /media --- the default mount point for any user-removable media (e.g. external disks, USB flash drives, memory card readers, optical disks, etc.) connected to the system
 USB flash drive with the label FlashDrive connected by the user john would be mounted under /media/john/FlashDrive/

 Whenever you need to manually mount a filesystem, it is good practice to mount it under /mnt. 

 The swap partition is used to swap memory pages from RAM to disk as needed.
```bash
swapon --show                                           # To check if the system uses any kind of swap areas

sudo mkswap [partition f.e. /dev/vdb3]                  # To mark the partition as swap
sudo swapon --verbose [partition f.e. /dev/vdb3]        # To swap on the system

sudo swapoff [partition f.e. /dev/vdb3]                 # To stop using partition as swap

You can also use file as swap:
sudo dd if=/dev/zero of=/swap bs=1M count=2048 status=progress        # creates a file of the necessary size (2GB) filled with zeros
# /dev/zero (basically infinite zeros) as an input file (if) and generates file named swap as an output file (of)
# it basically repeats 1 megabyte (1M) of zeros 2048 times (which is 2Gb)

sudo chmod 600 /swap                                                  # Give permission for only user to read and write in this file:
sudo mkswap /swap
sudo swapon --verbose /swap
sudo swapon --show
```

LVM
Logical Volume Management (LVM) is a form of storage virtualization that offers system administrators a more flexible approach to managing disk space than 
traditional partitioning. 

The basic unit is the Physical Volume (PV), which is a block device on your system like a disk partition or a RAID array.

Several Physical Volumes (PV) form Volume Group (VG), Volume Group can have multiple Logical Volumes (LV)

A Logical Volume will be created in /dev, named as /dev/VGNAME/LVNAME, where VGNAME is the name of the Volume Group, and LVNAME is the name of the Logical Volume.

```bash
sudo lvmdiskscan                                        # displays disks and partitions available

# physical volume commands
sudo pvdisplay                                                  # detailed info about physical volume
sudo pvs                                                        # lists physical volumes in the system
sudo pvcreate [filesystem1] [filesystem2]                       # creates physical volumes (pv) from multiple filesystems f.e. /dev/sde, /dev/sdd
                                                                # multiple pvs created if multiple filesystems given
sudo pvremove [pv name]                                         # removes physical volume

# volume group commands
sudo vgdisplay                                                  # detailed info about volume groups
sudo vgs                                                        # lists volume groups in the system
sudo vgcreate [vg name] [pv name1] [pv name2]                   # creates a single volume group using one or multiple volume groups
sudo vgextend [vg name] [pv name3]                              # adds another physical volume to already existing volume group
sudo vgreduce [vg name] [pv name]                               # deletes physical volume from volume group decreasing its size

# lv commands
sudo lvdisplay                                                  # detailed info about logical volume
sudo lvs                                                        # lists logical volumes in the system
sudo lvcreate --size [number]G --name [lv name] [vg name]       # creates logical volume with given size and name on a given volume group
sudo lvresize --extents 100%VG [vg name]/[lv name]              # extends the size of logical volume to that of volume group
sudo lvresize --size 2G [vg name]/[lv name]                     # resizes the volume of logical volume to 2GB
sudo lvresize --size +2G [vg name]/[lv name]                    # adds an additional size of 2GB to the logical volume
# -L — linear option, enables us to make use of multiple physical volumes if available in the volume group
sudo lvresize -L +2G [vg name]/[lv name]                        # adds an additional size of 2GB to the logical volume from any given phyical volume

sudo mkfs.xfs [path to lv]                                      # creates a file system on a given logical volume. path can be /dev/[vg name]/[lv name]
sudo lvresize --resizefs --size 2G [vg name]/[lv name]          # resizes the sizes of both logical volume and file system to 2GB
sudo resize2fs [path to lv]                                     # resizes the file of the file system to that of logical volume
# FILE SYSTEM SIZE CAN ONLY BE INCREASED!!! YOU CAN NOT DECREASE THEM
```
### Debian package management
```bash
dpkg -i [package name]                                          # installs a package with .deb extension
dpkg -r [package name]                                          # removes a package or packages (all other packages that depends on it should be deleted)
dpkg -P [package name]                                          # removes a package with the files associated with it (purge)
--force                                                         # You can forcefully install/delete package even if dependencies are not met
                                                                # Do not use --force unless you are absolutely sure of what you are doing
dpkg -I [package name]                                          # Gets detailed information about a .deb package
dpkg --get-selections                                           # Lists every package installed in a system
dpkg -L [package name]                                          # Lists every file installed by the given package
dpkg-query -S [path to file]                                    # Displays a package that owns given file. Searches among installed packages
dpkg-reconfigure [package name]                                 # Restore a package’s settings to its “fresh” state
```
### Advanced Package Tool (APT)
APT provides features like advanced search capabilities and automatic dependency resolution.

APT is not a “substitute” for dpkg. You may think of it as a “front end”, streamlining operations and filling gaps in dpkg functionality, like dependency resolution.

```bash
# apt-get   ----->        used to download, install, upgrade or remove packages from the system.
apt-get update                                # Retrieves information about new update packages, recommended before installing or upgrading software
apt-get upgrade                               # Automatically upgrades any installed packages to the latest versions available from the repositories
apt-get upgrade [package name]                # Upgrades a single package

apt-get install [package name]                # Installs package with all of its dependencies
apt-get install -f                            # Installs the missing dependencies

apt-get remove [package name]                 # Removes a package along with other packages that depend of the given package
apt-get purge [package name]                  # Removes a package with its configuration files
apt-get remove --purge [package name]         # Does the same as the previous command

apt-get clean                                 # Removes all cache files needed for installing and upgrading packages


# apt-cache ----->        used to perform operations, like searches, in the package index.
apt-cache search [pattern]                    # Searches for a packages that contain pattern either in package name, description or files provided
apt-cache show [package name]                 # Shows full information about the package

# apt-file  ----->        used for searching for files inside packages.
apt-get install apt-file                      # Installs apt-file
apt-file update                               # Updates package cache used for apt-file (run after installation)
apt-file list [package name]                  # Lists content of the file
apt-file search [file name]                   # Provides which (both installed and uninstalled) package contains the given file
```

### RPM Package Manager
The RPM Package Manager (rpm) is the essential tool for managing software packages on Red Hat-based (or derived) systems.
```bash
rpm -i [package name]                         # Installs a package
rpm -ivh [package name]                       # Installs a package(v-verbose, more info; h-prints # signs to aid visualisation)
rpm -U [package name]                         # Upgrades previous version of package to newer version
rpm -e [package name]                         # Removes installed package, remove dependent packages first or error rises

rpm -qa                                       # Lists all installed packages(qa-query all)
rpm -qf [path to the file]                    # Retrieves package that owns the file(qf-query file)
rpm -qi [package name]                        # Retrieves information about the installed package(qi-query info)
rpm -ql [package name]                        # Lists files inside installed package(ql-query list)
rpm -qip [package name]                       # Retrieves information about the package that hasnt been installed yet
rpm -qlp [package name]                       # Lists files inside the package that hasnt been installed yet
```

### YUM (YellowDog Updater Modified) Package Manager
```bash
yum install [package name]                    # Installs a package
yum remove [package name]                     # Removes a package
yum update                                    # Updates every package available for update
yum update [package name]                     # Updates a package
yum check-update                              # Checks for update for every installed package
yum check-update [package name]               # Checks if update is available for package

yum repolist all                              # Lists available repositories
yum search [pattern]                          # Lists packages whose name or summary contain the search pattern
yum whatprovides [path to file]               # Retrieves a package name that provides for the given file
yum info [package name]                       # Gives information about the package
repoquery -l [package name]                   # Lists files inside package

yum-config-manager --add-repo [URL to .repo file]        # adds repo to /etc/yum.repos.d/
# disabled repositories will be ignored when installing or upgrading software
yum-config-manager --disable [repo id]        # disables repo
yum-config-manager --enable [repo id]         # enables repo
# get repo id from repolist all command, use only the part before the first / (f.e. updates from updates/7/x86_64)

# Yum stores downloaded packages and associated metadata in a cache directory (usually /var/cache/yum)
yum clean packages                            # cleans cache from packages
yum clean metadata                            # cleans cache from metadata (or use 'all' to clean all)
```

### DNF
dnf is the package management tool used on Fedora, and is a fork of yum. 
```bash
dnf install [package name]                    # Installs package
dnf remove [package name]                     # Removes package
dnf upgrade                                   # Upgrades all packages in the system
dnf upgrade [package name]                    # Upgrades given package

dnf search [pattern]                          # Lists packages whose name or description contain given pattern
dnf info [package name]                       # Retireves detailed information about the package
dnf provides [file]                           # Retrieves a package that provides this file
dnf list --installed                          # Lists all installed packages
dnf repoquery -l [package name]               # Lists files inside package

dnf help [command]                            # Displays more information/parameters for the given command

dnf repolist                                  # Lists available packages
dnf repolist --enabled                        # Lists enabled packages
dnf repolist --disabled                       # Lists diabled packages

dnf config-manager --add-repo [url]           # Adds repository to /etc/yum.repos.d/
# Added repositories are enabled by default.
dnf config-manager --set-enabled [repo id]    # Enables repository
dnf config-manager --set-disabled [repo id]   # Disables repository
```

### Zypper
zypper is the package management tool used on SUSE Linux and OpenSUSE. 
```bash
zypper in [package name]                      # Installs a package (can also use install)
zypper in [path to a package]                 # Installs a package on a disk
zypper rm [package name]                      # Removes a package (can also use remove)

zypper refresh                                # Refreshes repo metadata
zypper update                                 # Updates installed packages
zypper list-updates                           # Lists updates without updating

zypper se [package name]                      # Searches for a package (can also use search)
zypper se -i                                  # Lists all installed packages
zypper se -i [package name]                   # Searches for a package among installed ones
zypper se -u [package name]                   # Searches for a package among non-installed ones
zypper se --provides [file]                   # Shows which package provides this file

zypper info [package name]                    # Returns information about package
zypper repos                                  # Lists repositories currently registered in your system

zypper modifyrepo -e [repo alias]             # Enables package (use alias from repos command)
zypper modifyrepo -d [repo alias]             # Disables package
zypper modifyrepo -f [repo alias]             # Enables auto-refresh for te given package
zypper modifyrepo -F [repo alias]             # Disables auto-refresh for the given package

zypper addrepo [repo URL] [repo alias]        # Adds repository to the list
# Added repositories are enabled by default
zypper removerepo [repo alias]                # Removes repository from the list
```
