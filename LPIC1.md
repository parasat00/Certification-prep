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

Archiving Files############################################################################################

# Tar operations:
# -c (or --create; creates archive); -x (or --extract; extract archive); -t (or --list; lists file inside archive)

# Tar options
# -v (or --verbose; shows progress in terminal);  -f (or --file; specifies archive name) 

tar -cvf [archive name].tar [file(s)]      # Creates an archive from file(s) or directory

tar -xvf [archive name].tar                 # Extracts content of archive to the current directory
tar -xvf [archive name].tar -C [directory]  # Extracts archive inside specified directory


# gzip compression is faster but compressed file size are bigger
# bzip2 compresses slower but compressed file takes less size

# To compress a file:
gzip [file]                                 # Compresses with gzip
bzip2 [file]                                # Compresses with bzip2

# To decompress a file
gunzip [file]                               # Decompresses with gzip
bunzip2 [file]                              # Decompresses with bunzip2

# For compression with tar:   -z   ---gzip;       -j  ---bzip2

tar -czvf [archive name].tar.gz [file(s)]   # Creates an archive and compresses it with gzip
tar -cjvf [archive name].tar.bz [file(s)]   # Creates an archive and compresses it with bzip2

# To decompress just swap -c with -x:
tar -xzvf [archive name].tar.gz             # Decompresses with gzip
tar -xjvf [archive name].tar.bz             # Decompresses with bzip2


# CPIO (copy in, copy out) used to process archive files such as *.cpio or *.tar files.

#It takes the list of files from the standard input (mostly output from ls)
ls | cpio -o > [archive name].cpio          # Creates an archive
# -o stands for output; create an ouput and redirect it into archive file

cpio -id < [archive name].cpio              # Extracts an archive
# -i  ---to perform the extract; -d  ---create the destination folder


# dd command used to copy data from one location to another

dd if=[file name] of=[copy name]             # Copies content of input file(if) into output file(of)
dd if=[file name] of=[copy name] status=progress       # Does the same; Progress is shown

dd if=[file name] of=[copy name] conv=ucase  # Copies content and capitalizes all of the text
```

### Use streams, pipes and redirects
Weight: 4
Standard Linux processes have three communication channels opened by default: the standard input channel (most times simply called stdin), the standard output channel (stdout) and the standard error channel (stderr). 

The numerical file descriptors assigned to these channels are 0 to stdin, 1 to stdout and 2 to stderr. Communication channels are also accessible through the special devices /dev/stdin, /dev/stdout and /dev/stderr.

```bash
cat /etc/ > [file]                        # Overwrites stdout to a file
cat /etc/ 1> [file]                       # Does the same as previous command

cat /etc/ >> [file]                       # Appends stdout output to a file

cat /etc/ 2> [file]                       # Overwrites stderr to a file

wc -c < [file]                            # Redirects content of file as input wc command
wc -c 0< [file]                           # Does the same

grep -r '^The' /etc/ &> [file]            # Both stdout and stderr are written to file
grep -r '^The' /etc/ > [file] 2>&1        # Does the same

# Files are overwritten by output redirects unless Bash option noclobber is enabled
# noclobber works means > doesnt work; >> works;
set -o noclobber                          # Enables noclobber 
set -C                                    # Does the same

set +o noclobber                          # Disables noclobber 
set +C                                    # Does the same

# Here Document
# The Here document redirect allows to type multi-line text that will be used as the redirected content. 
# << indicate Here doc

# The insertion mode will finish as soon as a line containing only the ending term is entered.
wc -c <<EOF                              # Passes lines of text until EOF line as redirect file
> Kiki do you love me?
> Hahaha
> EOF
28

# Here String
# The Here string method is much like the Here document method, but for one line only
wc -c <<<"How many characters in Here String?"   # MUST BE INSIDE QUOTES      
36

# Piping
# -to pass on output from one program as an input to the second program

cat /proc/cpuinfo | wc            # Pass the content of file as input for word counter
# cat  ---to redirect an output to a file and content not shown on screen
# tee  ---to redirect an output to a file and still see it on the screen
[some command] | tee [file name]     # works like redirect
[some command] | tee -a [file name]  # appends text to the end of file

# Only the standard output of a process is captured by a pipe.
# stderr needs to be redirected to stdout then to it can be stdin
make 2>&1 | tee [file name]          # Both stderr and stdout redirected to file

# Command substitution
mkdir `date +%Y-%m-%d`               # Date outputs current date and its then used as name
mkdir $(date +%Y-%m-%d)              # Does the same as previous command

# The output of a command can be stored in variable
OS=`uname -o`                        # Stores the output of command
echo $OS                             # Output: GNU/Linux

# xargs - an intermediate that employs the output of a program as the argument of another program
xargs -I IMG convert IMG -resize 25% [file]/IMG < inputNames.txt
# -I declares every entry from inputNames as IMG
# As a result 'convert IMG -resize 25% [file]/IMG' runs for every entry in inputNames

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

### Create, monitor and kill processes
Weight: 4

A process or task is an instance of a running program.
Jobs are processes that have been started interactively through a terminal, sent to the background and have not yet finished execution.
```bash
jobs                         # Lists active jobs (and their status) in your Linux system
jobs -l                      # The same output +it will show process id (pid) of a job
jobs -n                      # Lists only processes that have changed status since the last notification
jobs -p                      # Lists only process ids (pid)s
jobs -r                      # Lists only running jobs
jobs -s                      # Lists only stopped(suspended) jobs

job %[job id]                # Displays specific job
job %[string]                # Lists jobs whose command starts with the given string
job %?[string]               # Jobs whose command contains given string
job %+                       # Current job(last started in the background/suspended from the foreground)
job %%                       # Does the same as previous command
job %-                       # Displays the job that was %+ before the current one

fg %[job id]                 # Take the job to the foreground/makes it current job
bg %[job id]                 # Take the job to the background
kill %[job id/pid]           # Terminates a job by its job id or pid

pgrep [name]                 # Displays pid of process by its name
pidof [name]                 # Displays pid of process by its name

pkill [name]                 # Kills process by it name
killall [name]               # Kills all processes under given name

[command] &                  # Starts the command in a background f.e: cat 60 &

nohup [command] &            # Detaches command from current session/Command runs even after session closed
# nohup.out ---is the default file where stdout and stderr of nohup command will be saved
nohup [command] > [file] &   # Specify where to store output of nohup command

watch                        # Executes a program periodically (2 seconds by default)
watch -n [x]                 # Executes a program every x seconds
watch uptime                 # To monitor load average changes over time
watch free                   # To monitor changes in memory use over time

top                          # Process monitoring tool(works dynamically)
# top sorts the percentage of CPU time used by each process in descending order by default
# M      --Sort by memory usage.
# N      --Sort by process ID number.
# T      --Sort by running time.
# P      --Sort by percentage of CPU usage.
# R      --To switch between descending/ascending order.

# other keys to interact with top:
# ? or h      --Help.
# k           --Kill a process. enter PID of the process to be killed 
# r           --Change the priority (renice). Enter the nice value.Possible values range from -20 through 19
              # Only the superuser can set a negative or lower than the current one value. 
# u           --List processes from a particular user (by default processes from all users are shown).
# c           --Show programs' absolute paths 
# V           --Forest/hierarchy view of processes.
# t and m     --Change the look of CPU and memory readings respectively in a four-stage cycle:
            # the first two presses show progress bars, the third hides the bar and the fourth brings it back.
# W           --Save configuration settings to ~/.toprc

htop          # Fancier and more user-friendly version of top
atop          # version of top with more info

ps            # Shows a snapshot of processes statically
ps a          # Shows all processes with a terminal (tty)

# ps can accept options in three different styles: BSD, UNIX and GNU
# p --displays info about particular process
ps p [pid]    # BSD. Options do not follow any dash
ps -p [pid]   # UNIX. Options follow single dash
ps --p [pid]  # GNU. Options follow double dash

ps U [user]   # Displays processes started by user. BSD style
ps -u [user]  # The same command but in UNIX style
ps --u [user] # The same command but in GNU style

ps aux        # Displays processes from all shells (not only current shell) are shown
            # a      --Show processes that are attached to a tty or terminal.
            # u      --Display user-oriented format.
            # x      --Show processes that are not attached to a tty or terminal.

# GNU & tmux#####################################################################################

# GNU Screen
screen      # Invoke GNU screen, first window and session are created
screen -t [title]      # Creates a new window with title

Ctrl+a      # Screen's prefix
Ctrl+a+w    # Shows all windows
Ctrl+a+c    # Creates new window
Ctrl+a+A    # Sets a title for window

# You can move between windows
Ctrl+a+n    # Go to the next window
Ctrl+a+p    # Go to the previous window
Ctrl+a+[number]   # Go to specified window
Ctrl+a+"    # Lists windows. Select one you want to enter"

Ctrl+a+k    # Kills a window you are in

# screen can divide a terminal screen up into multiple regions
Ctrl+a+S    # Divides a window into regions horizontally
Ctrl+a+|    # Divides a window into regions vertically
Ctrl+a+Tab  # Move between regions
# You can create a new or move to already existing window inside region
# Use already mentioned commands to do so

Ctrl+a+X    # Terminates a region you are in
Ctrl+a+Q    # Terminates all regions except current
# Terminating a region does not terminate its associated window.

# windows and regions exist inside a session
screen -ls  # Lists sessions with their PID, name and status
screen -list # Does the same

screen -S "[name]"                      # Creates a new session with given name
screen -S [session PID/name] -X quit    # Kills a session
# You will be sent back to your terminal prompt outside of screen

Ctrl+a+d                                # Detaches a session

screen -r [session PID/name]            # Ataches/enters deatached session
screen -r                               # Can be used if there is single session

# To copy/paste in GNU Screen you enter scrollback mode:
Ctrl+a+[                                # Enters scrollback mode
# Move to the beginning of the piece of text you want to copy and mark it by pressing SPACE
# Move to the end of the piece of text you want to copy and press SPACE again
Ctrl+a+]                                # Pastes what you copied

# The system-wide configuration file for screen is /etc/screenrc.
# Alternatively, a user-level ~/.screenrc can be used.


# tmux

tmux                                    # Invoke tmux, first window and session are created
tmux ls                                 # Lists sessions
tmux new -s "[name]" -n "[window name]" # Creates new session and window with specified names

Ctrl+b                                  # tmux prefix
Ctrl+b+c                                # Create new window
Ctrl+b+,                                # Renames a window

Ctrl+b+w                                # Lists windows, select to switch
Ctrl+b+n                                # Switches to the next window
Ctrl+b+p                                # Switches to the previous window
Ctrl+b+[number]                         # Switches to the specified window

Ctrl+b+&                                # Kills a window

Ctrl+b+f                                # Finds window by name
Ctrl+b+.                                # Changes window's index number

# Panes are complete pseudo-terminals linked to a window.
# This means that killing a pane will also kill its pseudo-terminal and any associated programs running within
Ctrl+b+"                                # Splits a window horizontally"
Ctrl+b+%                                # Splits a window vertically

Ctrl+b+x                                # Destroys a current window
Ctrl+b+↑,↓,←,→                          # Move between panes
Ctrl+b+;                                # Move to the last active pane
Ctrl+b+Ctrl+↑,↓                         # Resizes by one line
Ctrl+b+Alt+↑,↓                          # Resizes by five lines
Ctrl+b+{                                # Swaps panes (current with previous)
Ctrl+b+}                                # Swaps panes (current with next)
Ctrl+b+z                                # Zoom in/out pane

Ctrl+b+t                                # Fancy clock inside pane (press q to kill)
Ctrl+b+!                                # Turn the pane into a window

tmux ls                                 # Lists sessions
Ctrl+b+s                                # Lists sessions, select to switch sessions
Ctrl+b +type ':new' in terminal         # Create new session
Ctrl+b+$                                # Renames a session
tmux kill-session -t [session-name]     # Kills a session
# If you type the command from within the current session, you will be taken out of tmux

tmux attach -t [session name]           # Attaches/enters a session
tmux at [session name]                  # Attaches/enters a session
tmux a [session name]                   # Attaches/enters a session
tmux a                                  # If there is a single session, session name not needed

Ctrl+b+d                                # Detaches from a session

tmux attach -d -t [session name]        # Dettaches session from any other terminal and attaches a session

Ctrl+b+D                                # Select client to detach
Ctrl+b+r                                # Refreshes client's terminal

# To copy/paste in tmux you enter scrollback mode:
Ctrl+a+[                                # Enters scrollback mode
# Move to the beginning of the piece of text you want to copy and mark it by pressing Ctrl+SPACE
# Move to the end of the piece of text you want to copy and press Alt+w
Ctrl+a+]                                # Pastes what you copied

# The configuration files for tmux are typically located at /etc/tmux.conf and ~/.tmux.conf. 
```
### Modify process execution priorities
Weight: 2

Every process has two predicates that intervene on its scheduling: the scheduling policy and the scheduling priority.

There are two main types of scheduling policies: real-time policies and normal policies. Processes under a real-time policy are scheduled by their priority values directly. 
If a more important process becomes ready to run, a less important running process is preempted and the higher priority process takes control of the CPU. A lower priority process will gain CPU control only if higher priority processes are idle or waiting for hardware response.

Any real-time process has higher priority than a normal process. 
As a general purpose operating system, Linux runs just a few real-time processes. Most processes, including system and user programs, run under normal scheduling policies. 
Normal processes usually have the same priority value, but normal policies can define execution priority rules using another process predicate: the nice value.

 Real-time processes have static priorities from 0 to 99 [0,99]
 Normal proccesses have static priorities from 100 to 139 [100, 139]

!!! Lower values mean higher priority

Every normal process begins with a default nice value of 0 (priority 120)
Nice numbers range from -20 (less nice, high priority) to 19 (more nice, low priority). 

```bash
grep ^prio /proc/[PID]/sched      # Displays static priority of an active process

# To see the priorities of all running process run either of theses commands:
ps -Al
ps -el
# To find actual priority of the process: Add 40 to it. (+40)

# Alternatively you can use top command
top
# In top command all proccesses' priorities are substracted by 100
# all priorities -100 --> real-time priorities: negative or rt; normal priorities: 0 to 39

# Every normal process begins with a default nice value of 0 (priority 120)
# To start a process with a non-standard priority:
nice -n 15 [command]              # Process starts with nice value of 15
# If -n is not present nice command changes the niceness to 10 by default

renice [new nice value] -p [PID]  # Change the priority of a running process
renice +[val] -g [group]          # Add val niceness to all processes owned by specific group
renice +[val] -u [user]           # Add val niceness to all processes owned by specific user

# Besides renice, the priority of processes can be modified with other programs, like the program top.
# Enter top, press "r", type the PID of process and then enter the new nice value
```

### Search text files using regular expressions
Weight: 3

- . (dot)		--Atom matches with any character.
- ^ (caret)		--Atom matches with the beginning of a line.
- $ (dollar sign)	--Atom matches the end of a line.
- [ab]              --Match any single character from the list(a or b)
- [0-9] or [a-z]    --Match any single character from the range
- [^ab]             --Matches none of the characters/Excludes
- (*)               --Previous character appears zero or more times
- (+)               --Previous character appears one or more times
- ?                 --Previous character appears zero or one time
- {i}               --Previous character appears exactly i times
- {i,}              --Previous character appears at least i times
- {,i}              --Previous character appears at most i times
- {i,j}             --Previous character appears i to j times
- i|j               --Matches i or j
- ()                --For grouping

**grep** is a pattern finder and **sed** is a stream editor. They are useful by themselves, but it is when working together with other processes that they stand out
```bash
grep [string] [file]             # searches for appearances of string inside file(case sensitive)
grep -i [string] [file]          # searches for string but case insensitive
grep -r [string] [dir]           # searches for string recursively inside given directory
grep -v [string] [file]          # prints everything that doesn't contain given string
grep -w [string] [file]          # command searches for appearance of the whole word 
grep -c [string] [file]          # only displays the total count for how many times a match occurs

grep -[number] [string] [file]   # includes [number] lines before and [number] lines after when it finds a line with a match
grep -C [number] [string] [file] # does the same
grep -A [number] [string] [file] # prints [number] lines that was after the line with specified string
grep -B [number] [string] [file] # prints [number] lines that was before the line with specified string

grep -f [regex file] [file]      # f indicates a file containing the regular expression to use
grep -n [string] [file]          # displays how many lines in the output (matched lines)
grep -H [string] [file]          # print also the name of the file containing the line
grep -z [string] [file]          # takes the input or output as a sequence of lines
# usually used in combination with the output from the find command using its -print0 option

# There are two complementary programs to grep: egrep and fgrep.
# The program egrep is equivalent to the command grep -E
# The program fgrep is equivalent to grep -F, it does not parse regular expressions
egrep                            # can use extended regular expressions
egrep -o [regex] [file]          # displays only the parts of a text stream that match the expression
fgrep                            # simple searches, matches a literal expression, it does not parse regular expressions


# Stream editor sed (previews without changing the file)
sed 's/[first word]/[second word]/g' [file]    # Swaps (s) every occurence of first word with the second word
                                               # g means globally, at every line at every occurence
sed -i 's/[first word]/[second word]/g' [file] # Swaps every first word with second word and saves output to original file
sed -i.backup 's/[first word]/[second word]/g' [file] # Swaps every first word with second word and saves output to file with name
                                                                                              # (original file name + .backup )

sed [n]d [file]                    # Prints every line except n-th line (d-delete)
sed [n],[m]d [file]                # Prints every line except lines from n to m (range)

# More than one instruction can be used in the same execution, separated by semicolons. F.e.:
sed "[n],[m]d;[x]d" [file]         # Prints every line exept lines from n to m and x-th line

sed "/[regex]/c [text]" [file]     # Replaces the matching line with the given text
sed "/[regex]/a [text]" [file]     # Inserts the given text to the next line after the matching line
sed "/[regex]/r [source]" [file]   # Inserts the content of the source file to next line(s) after the matching line
sed "/[regex]/w [dest]" [file]     # Overwrites destination file with the matching lines


sed -n /[word]/p [file]            # Lists only the lines containing the word (-n means dont print unless; p - print)
sed /[word]/d [file]               # Lists every line not containing word (d--delete)
sed -r ...                         # Enables to use extended regular expressions(-r)
sed 's/[word]/[some command]/e'... # e at the end of the expression tells sed to replace matches with the output of command
```

### Basic File Editing
Weight: 3
#### **VI**
#### Normal/Command mode
is the default mode when we open vi editor
Common and basic commands:
- i, I		--Enter the insert mode before the current cursor position and at the beginning of the current line
- d, dd           --Cut the selection or entire line
- 0, $		--Go to the beginning and end of the line.
- 1G, G		--Go to the beginning and end of the document.
- (, )		--Go to the beginning and end of the sentence.
- {, }		--Go to the beginning and end of the paragraph.
- w, W		--Jump word and jump word including punctuation.
- h, j, k, l	--Left, down, up, right.
- e or E		--Go to the end of the current word.
- /, ?		--Search forward and backwards.
- a, A		--Enter the insert mode after the current cursor position and at the end of the current line.
- o, O		--Add a new line and enter the insert mode in the next line or in the previous line.
- s, S		--Erase the character under the cursor or the entire line and enter the insert mode.
- c      		--Change the character(s) under the cursor.
- r		      --Replace the character under the cursor.
- x	      	--Delete the selected characters or the character under the cursor.
- v, V		--Start a new selection with the current character or the entire line.
- y, yy		--Copy (yanks) the character(s) or the entire line.
- p, P		--Paste copied content, after or before the current position.
- u      		--Undo the last action.
- Ctrl-R		--Redo the last action.
- ZZ      		--Close and save.
- ZQ      		Close and do not save.

vi can organize copied text in registers, allowing to keep distinct contents at the same time. 
A register is specified by a character preceded by " and once created it’s kept until the end of the current session.
We chose l as our character for register
- Select a word or line you want to copy to register
- type "ly (creates a register containing the selected word(s))
- type "lp to paste

Any key sequence can be recorded as a macro for future execution. For example, to surround a selected text in double-quotes.
- Select a word or line you want to copy to register
- press q and any character of your choice, macro will be associated with this character (f.e. d; we press qa and @d will appear in the footer line, indicating that the recording is on)
- Type commands you want to record
- press q again to end the recording
- Now commands you recorded will be executed everytime you type @d(character you chose) in command mode
```bash
vi [file]            # Opens file in vi editor
vi +[n] [file]       # Opens file in vi editor, cursor at the n-th line
vi + [file]          # Opens file in vi editor, cursor at the last line (by default)

:s/REGEX/TEXT/g      # Works the same as command sed, swaps matched text with TEXT
:q or :quit		   # Exit the program.
:quit! or :q!        # Exit the program without saving.
:wq			   # Save and exit.
:exit or :x or :e	   # Save and exit, if needed.
:visual		   # Go back to command mode(same effect-- : and press Enter)
```
#### Nano
A simpler alternative to vi is GNU nano
- Ctrl-6 or Meta-A      Start a new selection. It’s also possible to create a selection by pressing Shift and moving the cursor.
- Meta-6      		Copy the current selection.
- Ctrl-K      		Cut the current selection.
- Ctrl-U	      	Paste copied content.
- Meta-U      	      Undo.
- Meta-E	            Redo.
- Ctrl-\		      Replace the text at the selection.
- Ctrl-T      		Start a spell-checking session for the document or current selection.

Emacs is another very popular text editor for the shell environment. Emacs includes many features that make it more than just a text editor. It is also an IDE (integrated development environment) capable of compiling, running, and testing programs. Emacs can be configured as an email, news or RSS client, making it an authentic productivity suite.

Bash uses the session variables VISUAL or EDITOR to find out the default text editor for the shell environment. 
For example, the command **export EDITOR=nano** defines nano as the default text editor in the current shell session. 
To make this change persistent across sessions, the command should be included in **~/.bash_profile**.

### 104.1 Create partitions and filesystems
Weight:  2

All disk-related operations in this lesson need to be done as the user **root** (the system administrator), or with root privileges using **sudo**.

There are two main ways of storing partition information on hard disks:
* MBR
  - The partition table is stored on the first sector of a disk, called the Boot Sector, along with a GRUB boot loader.
  - Limit on disk size: 2TB
  - 4 primary partitions per disk
    On an MBR disk, you can have 2 main types of partitions, primary and extended. primary and extended partitions are treated exactly in the same way, so there are no “advantages” of using one over the other. extended partition acts as a container for logical partitions. You could have, for example, a primary partition, an extended partition occupying the remainder of the disk space and five logical partitions inside it.


* GUID
  - There is no practical limit on disk size.
  - The maximum number of partitions are limited only by the operating system itself. 
  - It is more commonly found on more modern machines that use UEFI instead of the old PC BIOS.
  Unlike MBR disks, when creating a partition on GPT disks the size is not limited by the maximum amount of contiguous unallocated space. You can use every last bit of a free sector, no matter where it is located on the disk.

The standard utility for managing MBR partitions on Linux is **fdisk**.
The utility **gdisk** is the equivalent of fdisk when dealing with GPT partitioned disks. I
```bash
fdisk [device]        # To edit the partition table of the device f.e: fdisk /dev/sda

# You can create, edit or delete partitions at will, but nothing will be written to disk unless you use the write (w) command.
w                     # Writes the changes to disk (changes are saved)
q                     # Quit/discard changes
p                     # Prints current partition table
n                     # Creates a partition at the start of unallocated space on the disk.
F                     # Displays free/unallocated space on the disk
d [parttion's number] # Deletes a partition
d                     # Deletes a partition, works if there is only one partition on the disk
t [parttion's number] # Changes type of the partition, partition type must be specified by its corresponding hexadecimal code
l                     # Lists all the valid codes, F.e. Linux partitions are type 83 (Linux), swap partitions are type 82 (Linux Swap).

gdisk [device]        # To edit the GUID partition table of the device f.e: gdisk /dev/sda
p                     # Prints current partition table, also prints free space so no need to use F command
n                     # Creates a partition, also need to specify partition type
l                     # Lists all the valid codes
d [parttion's number] # Deletes a partition'
# Unlike fdisk, the first partition will not be automatically selected if it is the only one on the disk
s                     # Sorts/reorders disks,partitions. Useful when partition is deleted and numbers need to be reordered

r                     # Accesses recovery task menu
      b               # Rebuild a corrupt main GPT header
      c               # Rebuild a corrupt main GPT partition table
      d               # Use the main header to rebuild a backup
      e               # Use the table to rebuild a backup
      f               # Convert a MBR to a GPT
      g               # Convert a GPT to a MBR
      ?               # Lists available recovery commands in recovery mode

# The Extended Filesystem (ext) was the first filesystem for Linux
# The utilities mkfs.ext2, mkfs.ext3 and mkfs.ext4 are used to create ext2, ext3 and ext4 filesystems.
mkfs.ext4 [device]      # Creates ext4 file system. f.e.: mkfs.ext4 /dev/sdb1
mke2fs -t ext4 [device] # Does the same

# mke2fs parameters. All of them also apply to mkfs.ext2, mkfs.ext3 and mkfs.ext4:
      -b [size]         # Sets the size of the data blocks
      -c                # Check the target device for bad blocks before creating the filesystem.
      -c -c             # Much slower check
      -d [directory]    # Copies the contents of the directory to the root of the new filesystem
      -F                # Will force mke2fs to create a filesystem (dangerous)
      -L [volume_label] # Will set the volume label. The label must be at most 16 characters long.
      -n                # Simulates the creation of the filesystem
      -q                # Quiet mode. mke2fs will not produce any output to the terminal.
      -U [id]           # Sets the UUID (Universally Unique Identifier) of a partition
      -V                # Verbose


# XFS is a high-performance file system, due to its performance and reliability features,
# it is commonly used for servers and other environments that require high (or guaranteed) filesystem bandwidth
mkfs.xfs [device]       # Creates xfs file system. f.e.: mkfs.xfs /dev/sdb1

# As in mke2fs, mkfs.xfs supports a number of command line options:
      -b size=[value]   # Sets the block size on the filesystem, in bytes
      -m crc=[value]    # m-metadata option, crc-checks to verify the integrity of all metadata on the disk, value can be 1 or 0
      -m uuid=value     # Sets the UUID of a partition
      -f                # Force the creation of a filesystem on the target device even if a filesystem is detected on it
      -l logdev=[device]# This will put the log section of the filesystem on the specified device, instead of inside the data section.
      -l size=[size]    # Sets the size of the log section
      -q                # Quiet mode
      -L [label]        # Sets the label for filesystem. At most 12 characters
      -N                # Simulates the creation of the filesystem

# The FAT filesystem originated from MS-DOS, and through the years has received many revisions.
# The FAT32 format released in 1996 with Windows 95 OSR2.

# VFAT is an extension of the FAT16 format with support for long (up to 255 characters) file names.
# Both filesystems are handled by the same utility, mkfs.fat. mkfs.vfat is an alias to it.

mkfs.fat [device]       # Creates file system. f.e.: mkfs.fat /dev/sdb1
      -c                # Checks the target device for bad blocks before creating the filesystem
      -C [file] [blocks]# Creates the file and then create a FAT filesystem inside it, effectively creating an empty “disk image”
      # “disk image” can be later written to a device using a utility such as dd or mounted as a loopback device
      -F [size]         # Selects the size of the FAT (File Allocation Table), between 12, 16 or 32 versions
      # If not specified, mkfs.fat will select the appropriate option based on the filesystem size.
      -n [name]         # Sets the volume label, or name, for the filesystem. Can be up to 11 characters long, and the default is no name
      -v                # Verbose
# mkfs.fat cannot create a “bootable” filesystem. According to the manual page, “this isn’t as easy as you might think” and will not be implemented.

# exFAT is a filesystem created by Microsoft in 2006 that addresses one of the most important limitations of FAT32: file and disk size.
# On exFAT, the maximum file size is 16 exabytes (from 4 GB on FAT32), and the maximum disk size is 128 petabytes.

mkfs.exfat [device]     # Creates file system. f.e.: mkfs.exfat /dev/sdb1
      -i [volume id]    # Sets the Volume ID. This is a 32-Bit hexadecimal number. If not defined, an ID based on the current time is set.
      -n [name]         # Sets the volume label, or name, for the filesystem. Can be up to 15 characters long, and the default is no name
      -p [sector]       # Specifies the first sector of the first partition on the disk. This is an optional value, and the default is zero.
      -s [sectors]      # Defines the number of physical sectors per cluster of allocation. This must be a power of two, like 1, 2, 4, 8, and so on.

# Btrfs Filesystem
# There are many features that make Btrfs attractive on modern systems where massive amounts of storage are common.
mkfs.btrfs [device]     # Creates btrfs file system. f.e.: mkfs.btrfs /dev/sdb
      -L                # Sets a label (or name) for your filesystem. Can have up to 256 characters, except for newlines
      -m [parameter]    # Specifies how metadata will be distributed in the disk array
      # Valid parameters are raid0, raid1, raid5, raid6, raid10, single and dup

# Subvolumes are like filesystems inside filesystems. Think of them as a directory which can be mounted as (and treated like) a separate filesystem.
btrfs subvolume create [filesystem]         # Creates subvolume
btrfs subvolume show [filesystem]           # Shows info about subvolume

# Snapshots are just like subvolumes, but pre-populated with the contents from the volume from which the snapshot was taken.
btrfs subvolume snapshot [origin] [copy]    # Creates a snapshot of the Btrfs filesystem mounted in [origin] directory inside [copy] directory
btrfs subvolume snapshot -r [origin] [copy] # Creates a read-only snapshot of the Btrfs filesystem 


# GNU Parted is a very powerful partition editor (hence the name) that can be used to create, delete, move, resize, rescue and copy partitions.
# It can work with both GPT and MBR disks, and cover almost all of your disk management needs.

# Unlike fdisk and gdisk, parted makes changes to the disk immediately after the command is issued, without waiting for another command to write the changes to disk.

parted [device]      # The program starts an interactive command line interface like fdisk and gdisk with a (parted) prompt for you to enter commands
# F.e: parted /dev/sda
# Be careful! If you do not specify a device, parted will automatically select the primary disk (usually /dev/sda) to work with.

select [device]      # To switch to a different disk than the one specified on the command line

print                # Prints information about the currently selected partition
print devices        # Lists all block devices connected to your system
print all            # Prints information about all connected devices
print free           # Prints how much free space there is

mklabel [msdos/gpt]  # Creates a partition table on an Empty disk. msdos for MBR, gpt for GPT

mkpart [parttype] [fstype] [start] [end] # Creates a partition
      [parttype]     # Partition type, can be primary or extended if MBR is selected
      [fstype]       # Specifies which filesystem will be used on this partition.
      [start]        # Specifies the exact point on the device where the partition begins
      [end]          # Specifies the end of the partition.
                     # Note that this is not the size of the partition, this is the point on the disk where it ends.
# Example of mkpart:
mkpart primary ext4 1m 100m

rm [number]          # Removes a partition. Partition number you can get from print command
rescue [start] [end] # Recovers deleted partition. Using its previous start and end points
# rescue can only recover partitions that have a filesystem installed on them. Empty partitions are not detected

# Resizing partitions
# During resizing the partition must be unused and unmounted.
# You need enough free space after the partition to grow it to the size you want.
resizepart [number] [size] # Changes given partition's size
resize2fs [device] [size]  # Changes size of the filesystem
# If you omit the size parameter, it will use all of the available space of the partition.
# Before resizing, it is advised to unmount the partition.
resize2fs -M [device]      # Adjusts the size of the filesystem so it is just big enough for the files on it

# Pay attention when shrinking partitions. You must shrink the filesystem first and then partition
# If you get the order of things wrong, you will lose data!
```

### 104.2 Maintain the integrity of filesystems
Weight: 2

In this lesson, we will discuss the tools available to monitor filesystem usage, optimize its operation and how to check and repair damage
```bash
du       # To check how much space is being used and how much is left on a current directory and all its subdirectories
du -h    # Human readable du (in megabytes)
du -ah   # Shows individual usage of all the files in a directory
du -Sh   # Shows usage of subdirectories and current directory without its subdirectories
du -Shc  # The same command + shows a grand total at the end
du -h -d1 # (d)Deep 1 level. Shows usage of current directory and its subdirectories (not subdirectories of subdirectory)
du -ah --exclude=[pattern] # Excludes files whose name matches the pattern

# du works at files level. df works at filesystem level
df             # Provides a list of all of the available (already mounted) filesystems on your system
df -h          # Human readable df (in megabytes)
df -i          # Shows used/available inodes instead of blocks
df -T          # Prints types of filesystem
df -t [type]   # Print only filesystems of the given type
df -x [type]   # Exclude filesystems of the given type
df --output=[parameter] # Customizes what should be displayed on output
# source--filesystem, fstype--type of filesystem, size, used--space used, avail--available space. pcent--percent of usage, target--where fs mounted
# itotal--total number of indoes, iused--number of inodes used, iavail--number of available inodes, ipcent--percentage of used inodes
# F.e:
      df -h --output=target,source,fstype,pcent,itotal,iused

fsck [device]              # Filesystem check for ext2, ext3 and ext4 (f.e. device=/dev/sdb1)
# NEVER run fsck (or related utilities) on a mounted filesystem. If this is done anyway, data may be lost.

fsck -t [fs] [device]      # Specifies filesystem on a device, F.e: fsck -t vfat /dev/sdb1
fsck.msdos [device]        # Calls filesystem specific utility. Alternative for previous command

# Command-line arguments for fsck:
# -A  --Checks all filesystems listed in /etc/fstab
# -C  --Displays progress bar (works only for ext2/3/4)
# -N  --Simulation, prints what would be done without actually checking it
# -AR --Skips checking of the root filesystem
# -V  --Verbose

# The specific utility for ext2, ext3 and ext4 filesystems is e2fsck, also called fsck.ext2, fsck.ext3 and fsck.ext4 (those three are merely links to e2fsck).
# When a filesystem error is found, fsck stops and asks the user what to do.
# The user must type y to fix the problem, n to leave it unfixed or a to fix the current problem and all subsequent ones.
# Options for e2fsck to run in non-interactive mode:
# -f	Forces e2fsck to check a filesystem even if it is marked as “clean”, i.e. has been correctly unmounted
# -y	This will answer y (yes) to all questions.
# -n	Answers n (no) to all questions, this will cause the filesystem to be mounted read-only, so it cannot be modified
# -p	Attempts to automatically fix any errors found. If an error that requires intervention from the system administrator is found,
      # e2fsck will provide a description of the problem and exit.

# tune2fs --utility used to display and change parameters of ext filesystems
tune2fs -l [device]                  # Displays current parameters for the given device (f.e device=/dev/sdb1)
tune2fs -c [number] [device]         # Sets maximum mount count
tune2fs -C [number] [device]         # Sets the number of times system has been mounted
# Number of times system has been mounted increases each time fs is mounted. If it reaches maximum mount count fsck will run on next boot

tune2fs -i [number][letter] [device] # Sets time interval between checks. 
# F.e: tune2fs -i 10d /dev/sdb1 will check fs at next reboot every 10 days (d-days, m-months, y-years)
tune2fs -i 0 [device]                # Disables the feature

tune2fs -L [label] [device]          # Sets label for fs. Up to 16 characters long
tune2fs -U [uuid] [device]           # Sets uuid for device
# Both the label and UUID can be used instead of the device name (like /dev/sda1) to mount the filesystem.
tune2fs -e [behaviour] [device]      # Sets the kernel behavior when a filesystem error is found. Possible behaviours:
      # continue      --Will continue execution normally.
      # remount-ro    --Will remount the filesystem as read-only.
      # panic	    --Will cause a kernel panic.

# The default behavior is to continue.
# remount-ro might be useful in data-sensitive applications, as it will immediately stop writes to the disk, avoiding more potential errors.

# ext3 filesystems are basically ext2 filesystems with a journal.
tune2fs -j [device]                  # Adds journal to ext2 fs converting it to ext3
# Afterwards, when mounting the converted filesystem, do not forget to set the type to ext3 so the journal can be used.

# -J allows you to use extra parameters to set some journal options
tune2fs -J size=[size] [device]      # Sets the journal size in megabytes
tune2fs -J location=[loc] [device]   # Sets where journal should be stored(suffix for specific block M, specific position on the disk G)
tune2fs -J device=[loc] [device]     # Puts journal on external device
# Multiple parameters can be set at once using comma as delimeter:
tune2fs -J size=10,location=100M,device=/dev/sdb1 /dev/sdc1

tune2fs -f                           # Forcefully completes opertaion. Can be DANGEROUS


# For XFS filesystems, the equivalent of fsck is xfs_repair
xfs_repair -n [device]               # Checks filesystem (n stands for no modify)
xfs_repair [device]                  # Checks and repairs 

# Command line options for xfs_repair:
      # -l [log dev] and -r [real-time dev] --if fs has external log and real time seactions
      # -m [n] --limits memory usage of xfs_repair to n megabytes. Can be useful on server settings
      # By default xfs_repair will scale its memory usage as needed, up to 75% of the system’s physical RAM
      # -d --will enable the repair of the filesystems that are mounted read-only
      # -v --Verbose. -v -v  --more verbose

# xfs_repair is unable to repair filesystems with a “dirty” log. You can “zero out” a corrupt log with the -L parameter
# This is a last resort as it may result in filesystem corruption and data loss.

# xfs_db is a utility to debug a filesystem. Mostly used to inspect various elements and parameters of the filesystem.F.e.:
xfs_db /dev/sdc1

# xfs_fsr is utility to reorganize(defragment) a XFS file system. When executed without any extra arguments >>>
# it will run for two hours and try to defragment all mounted, writable XFS filesystems listed on the /etc/mtab/ file.
```

### 104.3 Control mounting and unmounting of filesystems
Weight: 3

Before a filesystem can be accessed on Linux, it needs to be mounted.
Mounting -attaching the filesystem to a specific point in your system’s directory tree, called a mount point. Filesystems can be mounted manually or automatically and there are many ways to do this.
```bash
mount -t [type] [device] [mountpoint]      # Manually mounts a filesystem
      # [type] -type of filesystem to mount(f.e ext4, xfat, btrfs);  [device] -name of the partition containing filesystem(f.e: /dev/sdb1)
      # [mountpoint] -where filesystem will be mounted; Example of a command: mount -t exfat /dev/sdc1 ~/flash/

# label and UUID are specified when the filesystem is created and will not change, unless the filesystem is destroyed or manually assigned a new label or UUID.
lsblk -f [device]          # Queries information about a filesystem and find out the label and UUID associated with it

mount -t [type] UUID=[uuid] [mountpoint]   # Manually mounts a filesystem with the uuid of fs
mount -t [type] LABEL=[label] [mountpoint] # Manually mounts a filesystem with the label of fs

mount                      # Lists filesystems currently mounted on your system
mount -t [type]            # Prints only filesystems of the given type
mount -t [type],[type]     # The same operation can be used to filter output for multiple types
# The output of these command will be in this format: [source] on [target] type [type] [options]
# [source] -partition containing fs; [target] -directory where its mounted; [options] -options passed to mount command

# Command line parameters that can be used with mount:
      # -a                  mounts all filesystems listed in the file /etc/fstab
      # -o (or options)     passes a list of comma-separated mount options to the mount command, which can change how the filesystem will be mounted
      # -r (-ro)            mounts filesystem as read only
      # -w (-rw)            mounts filesystem as writable

umount [device/mountpoint] # Unmounts a filesystem (device name or mountpoint can be used)
      # umount /dev/sdc1
      # umount ~/flash/    # Both do the same thus interchangable

# Some of the command line parameters to umount are:
      # -a                  unmounts all filesystems in /etc/fstab
      # -f                  forceful unmount. Useful when unmounting a remote fs that became unreachable
      # -r                  if the filesystem cannot be unmounted, it will try to make it read only

# When unmounting a filesystem, you may encounter an error message stating that the target is busy.
# This will happen if any files on the filesystem are open.
lsof [device]              # lists processes accessing files of this filesystem and which files are open

# Traditionally, /mnt was the directory under which all external devices would be mounted
# This has been superseded by /media, which is now the default mount point for any user-removable media like
# external disks, USB flash drives, memory card readers, etc. connected to the system.
# On most modern Linux distributions and desktop environments, removable devices are automatically mounted under /media/[user]/[label]

# That being said, whenever you need to manually mount a filesystem, it is good practice to mount it under /mnt


# The text file /etc/fstab contains descriptions about the filesystems that can be mounted. Each line in the file follows this order:
# [filesystem] [mountpoint] [type] [options] [dump] [pass]
      # [filesystem]       device (f.e. /dev/sdc1) containing fs to be mounted. Instead of device you can specify UUID or label
      # [mountpoint]       where the filesystem will be mounted
      # [type]             type of fs
      # [options]          options to pass during mount
      # [dump]             usually zero, meaning to ignore, indicates whether any ext2, ext3 or ext4 filesystems should be considered for backup
      # [pass]             usually zero, if not zero, defines the order in which the filesystems will be checked on bootup

# options are a comma-separated list of parameters. Some of the options that are common for many filesystems:
      # atime and noatime  ---by default, access time is updated every time file is read, disabling it (noatime) can speed up disk I/O
      # auto and noauto    ---Whether the filesystem can be mounted automatically by mount -a command or not
      # dev and nodev      ---Whether character and block devices in the mounted fs should be interpreted or not
      # exec and noexec    ---Grant or deny permission to execute binaries on the file system
      # user and nouser    ---Allow (or not) ordinary user to mount the filesystem
      # group              ---Allows user to mount the fs if user belongs to the group owning the device that contain fs
      # owner              ---Allows user to mount the fs if user owns the device containing fs
      # suid and nosuid    ---Allows (or not) SETUID and SETGUID to take effect
      # ro and rw          ---Mount filesystem as read only or writable
      # remount            ---This will attempt to remount an already mounted filesystem. This is not used on /etc/fstab, but as a parameter to mount -o.
      # sync and async     ---Whether to do all I/O operations to the filesystem synchronously or asynchronously. async is usually the default.
      # defaults           ---Passes the default options: rw, suid, dev, exec, auto, nouser and async

# To mount a disk with systemd you need to create a mount unit inside /etc/systemd/system directory
# Mount units are simple text files with the .mount extension. The basic format will be like this:
      [Unit]
      Description=External data disk           # Short description of the mount unit

      [Mount]
      What=/dev/disk/by-uuid/56C11DCC5D2E1334  # What should be mounted
      Where=/mnt/external                      # Full path to where the volume should be mounted
      Type=ntfs                                # Type of fs
      Options=defaults                         # Mount options

      [Install]
      WantedBy=multi-user.target               # Used for dependency management.
      # Means that whenever the system boots into a multi-user environment (a normal boot) the unit will be mounted

# To work correctly, the mount unit must have the same name as the mount point.
# In this case, the mount point is /mnt/external, so the file should be named mnt-external.mount.

# After that, you need to restart the systemd daemon and start the unit
systemctl daemon-reload
systemctl start mnt-external.mount
# Now the contents of the external disk should be available on /mnt/external.


# Mount units can be automounted whenever the mount point is accessed.
# To do this, you need an .automount file, alongside the .mount file describing the unit.
# The basic format for .automount:
      [Unit]
      Description=Automount for external data disk   # Short description of the mount unit

      [Automount]
      Where=/mnt/external                            # Full path to where the volume should be mounted

      [Install]
      WantedBy=multi-user.target                     # Used for dependency management.
# Save the file with the same name as the mount point (in this case, mnt-external.automount),
# reload systemd and start the unit:
systemctl daemon-reload
systemctl start mnt-external.automount
```

### 104.5 Manage file permissions and ownership
Weight: 3

Every file on disk is owned by a user and a user group and has three sets of permissions: one for its owner, one for the group who owns the file and one for everyone else.

```bash
$ ls -l
drwxrwxr-x 2 carol carol   4096 Dec 10 15:57 Another_Directory
```
+ The first column on the listing shows the file type and permissions. For example, on drwxrwxr-x
      - The first character, d, indicates the file type.
      - The next three characters, rwx, indicate the permissions for the owner of the file, also referred to as user(**u**)
      - The next three characters, rwx, indicate the permissions of the group owning the file, also referred to as **g**
      - The last three characters, r-x, indicate the permissions for anyone else, also known as others or **o**
+ The second column indicates the number of hard links pointing to that file
+ The third and fourth columns show ownership information: the user and group that own the file.
+ The fifth column shows the file size
+ The sixth column shows the date and time the file was last modified
+ The seventh and last column shows the file name.

Common filetypes:
* - (normal file)
* d (directory)
* l (symbolic link)
* b (block device)
* c (character device)
* s (socket)

!!! Do not alter any of the permissions on block devices, character devices or sockets, unless you know what you are doing. This may prevent your system from working!

```bash
ls -ld [directory]            # Shows information about the directory(not content)
```

Permissions:
* r      --Stands for read and has an octal value of 4. Permission to open a file and read its contents. For directories: permission to read the directory’s contents, like filenames. But it does not imply permission to read the files themselves.
* w      --Stands for write and has an octal value of 2. Permission to edit or delete a file. For directories: this means permission to create or delete files in a directory.
* x      --Stands for execute and has an octal value of 1. This means that the file can be run as an executable or script. For directories: permission to enter a directory, but not to list its files (for that r is needed).

First the system checks if the current user owns the file, and if this is true it applies the first set of permissions only. Otherwise, it checks if the current user belongs to the group owning the file. In that case, it applies the second set of permissions only. In any other case, the system will apply the third set of permissions.

```bash
chmod [permission] [file]      # change permissions of the file
```
Only the owner of the file, or the system administrator (root) can change the permissions on a file

Modify file permissions:
* Symbolic Mode
      + --grants permission, - --revokes permission, = --sets to a specific value
      ```bash
        chmod g+w [file]      # grants permission to write(change) a file for group owners
        chmod u-r [file]      # revokes read permission from owner
        chmod a=rw- [file]    # grants all users exactly read, write and no execution permission
        chmod u+rw,o-x        # modifies multiple permissions at the same time
      ```
* Octal Mode
      ```bash
        # r-4      w-2      x-1       F.e: rwx = 4+2+1=7
        chmod 660 [file]      # modifies file permisssion, equivalent to: u=rw-,g=rw-,o=
        chmod 751 [file]      # equivalent to: u=rwx,g=r-x,o=--x
      ```
```bash
chmod -R u+rwx [file]         # modifies permissions for all files inside a directory and its subdirectories
```

Modifying file ownership

Unless you are the system administrator (root), you cannot change ownership of a file to another user or group you do not belong to.
user who owns a file does not need to belong to the group who owns a file
```bash
      chown [user]:[group] [file/dir]      # Modifies ownership of a file or directory
      chown :[group] [file/dir]            # Modifies only group owning a file/directory
      chown [user]: [file/dir]             # Modifies only user owning a file/directory
      chown [user] [file/dir]              # Does the same as previous

      chgrp [group] [file/dir]             # Changes group of a file/directory      
```

Querying Groups

```bash
      getent group                         # Lists groups that exist on a system
      groups [user]                        # Lists groups a user belongs to
      groupmems -g [group] -l              # Lists users in a given group
```

Default Permissions

They come from the user mask or **umask**, which sets the default permissions for every file created.
```bash
      umask                                # Displays default permissions
      umask -S                             # Displays default permissions in symbolic mode
      umask u=rwx,g=rwx,o=                 # Modifies default permissions for current shell session
      # Every new directory will inherit the permissions rwxrwx---, and every file rw-rw---- (as they do not get execute permissions)
```
| Value | Permission for Files | Permission for Directories |
| :---: | :------------------: | :------------------------: |
|   0   |   rw-                |  rwx                       |
|   1   |   rw-                |  rw-                       |
|   2   |   r--                |  r-x                       |
|   3   |   r--                |  r--                       |
|   4   |   -w-                |  -wx                       |
|   5   |   -w-                |  -w-                       |
|   6   |   ---                |  --x                       |
|   7   |   ---                |  ---                       |

Special Permissions

Besides the read, write and execute permissions for user, group and others, each file can have three other special permissions which can alter the way a directory works or how a program runs. 
+ Sticky Bit

      The sticky bit, also called the restricted deletion flag, has the octal value 1 and in symbolic mode is represented by a **t** within the other’s permissions.
      **This applies only to directories**, and has no effect on normal files. **On Linux it prevents users from removing or renaming a file in a directory unless they own that file or directory**
      Directories with the sticky bit set show a t replacing the x on the permissions for others on the output of ls -l
      
```bash
chmod 1755 Another_Directory         # Sets sticky bit
ls -ld Another_Directory
drwxr-xr-t 2 carol carol 4,0K Dec 20 18:46 Another_Directory
```
      
+ Set GID

      Set GID, also known as SGID or Set Group ID bit, has the octal value 2 and in symbolic mode is represented by an **s** on the group permissions.
      This can be applied to executable files or directories. 
      When applied to directories, it will make every file or directory created under it inherit the group from the parent directory.
      Files and directories with SGID bit show an s replacing the x on the permissions for the group on the output of ls -l:
      
```bash
cd Sample_Directory/
touch newfile
ls -lh newfile
-rw-r--r-- 1 carol carol 0 Jan 18 17:11 newfile
sudo chmod g+s Sample_Directory/
ls -ldh Sample_Directory/
drwxr-sr-x 2 carol users 4,0K Jan 18 17:17 Sample_Directory/
cd Sample_Directory/
touch emptyfile
ls -lh emptyfile
-rw-r--r-- 1 carol users 0 Jan 18 17:20 emptyfile      
# SGID bit made the file inherit the group owner of its parent directory, which is users
```
      
+ Set UID

      SUID, also known as Set User ID, has the octal value 4 and is represented by an **s** on the user permissions in symbolic mode.
      It only applies to files and has no effect on directories. Its behavior is similar to the SGID bit, but the process will run with the privileges of the user who owns the file.            Files with the SUID bit show a s replacing the x on the  permissions for the user on the output of ls -l:
      
```bash
ls -ld test.sh
-rwsr-xr-x 1 carol carol 33 Dec 11 10:36 test.sh
```
      
  You can combine multiple special permissions on one parameter. So, to set SGID (value 2) and SUID (value 4) in octal mode for the script test.sh with permissions 755, you would type:
      
```bash
chmod 6755 test.sh
ls -lh test.sh
-rwsr-sr-x 1 carol carol 66 Jan 18 17:29 test.sh
```
