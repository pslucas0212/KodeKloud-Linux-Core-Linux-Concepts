# KodeKloud Linux Basics: Linux Core Concepts

[KodeKloud Linux Basics Course Notes Table of Contents](https://github.com/pslucas0212/LinuxBasics)

### Linux Kernel
The kernel is the major compont of an OS.  It is the interface between applciations/processes and the underlying h/w, CPU, Memory, Storage, I/O.  The kernel manages and schedules resources.

Kernel is responsible for 4 major tasks
  - Memory Mangement - keep track of how much is consmed and where things are stored
  - Process managment - who can use the CPU when and for how long
  - Device Drivers 
  - Systems Calls and security

Kernel is monolithic - carries out the work (CPU and memomry management, etc.) above by itself   
Kernel is modular in that the kernel can extend its capability by loading dynamic extenxsions to the kernel   																			
Hardware <--- Kernel Space (([Device Drivers)([kernel)) <--- System calls ---- User Space (Applications/Programs)  

To see kernel name type
```
$ uname
Linux
```

To see kernel version type (debian example) add the -r
```
$ uname -r
5.10.63-v7l+
```
Example from KodeKLoud
```
$ uname -r
4.15.0-72-generic
```
Number | Meaning
-------|--------
4 | Kernel version
15 | Majgor release
0| Minor release
72 | Patch
Generic | Distro specific a

'kernel version.Major version.Minor vers.patch release' and may include distro specif information
	
	
 See the kernel.org webssite to get more information on the Linux kernel

### Kernel and User Space
Memory management is one of the most task of the kernel
Memory is divided into two areas: ***Kernel space*** and ***User space***. These are synomou to kerne and user mored
  - **Kernel space** conatins the kernel (code and extensions) and hardware device drives and a process running in the kernel space has unrestricted access to hardware and runs the Kernel code, kernel extensions and device drivers
  - **User space** contains application and programs - all process running outside kernel space which restricts access to cpu and memory
    - User space applications - user space also called user land
    - Applications get access to data and code in memory and/or on disk by making system calls to the kernel (see following examples)
	- Opening a file 
	- write a file
	- list process
	- Allocating memory by defining a variable
    - examples: programs written in Java, C, Python
	
All user programs function by manipulating data.  Data for users stored in memory or disk.  Applications in the user space access data by making system calls to the kernel space, which in turn makes calls to device drivers to the underlying physical hardware

Common system calls include getpid(), open(), close(), closedir(), readdir(), strlen()

### Working with Hardware
When attaching a device like a USB disk to the system generates a kernel event where the device driver for the USB disk is loaded into the kernel space.  The OS dectects the change and generates an event. The event is known as a uevent which is sent to the user device management system daemon called udev in the user space.  The udev services dynamically creates device node created on the device folder /dev/usb


dmesg is a tool that gets messages from the ring buffer.  Searching for specific keywords example follows.
```
$ dmesg | grep -i usb
[    0.152156] usbcore: registered new interface driver usbfs
[    0.152215] usbcore: registered new interface driver hub
[    0.152290] usbcore: registered new device driver usb
...
```
- RHEL Example below
```
$ dmesg | grep -i usb
[    0.115188] ACPI: bus type USB registered
[    0.115188] usbcore: registered new interface driver usbfs
[    0.115188] usbcore: registered new interface driver hub
[    0.115188] usbcore: registered new device driver usb
...
```
The udevadm utitily is an admin tool can query the udev database.  Example from debian that has a USB device loaded.
	
```
$ udevadm info --query=path --name=/dev/ttyUSB0
/devices/platform/scb/fd500000.pcie/pci0000:00/0000:00:00.0/0000:01:00.0/usb1/1-1/1-1.3/1-1.3:1.0/ttyUSB0/tty/ttyUSB0
```

udevadm monitor is used for monitoring udev events especially when attaching or removing devices
```
 $ udevadm monitor
monitor will print the received events for:
UDEV - the event which udev sends out after rule processing
KERNEL - the kernel uevent


```

The lspci command- list info about pci devices - like network cards, video cards, wireless cards and any device that attaches directly to the mother board via PCI slots.  Deiban example below.  PCI stands for peripheral component interconneect
```
$ lspci
00:00.0 PCI bridge: Broadcom Limited Device 2711 (rev 10)
01:00.0 USB controller: VIA Technologies, Inc. VL805 USB 3.0 Host Controller (rev 01)

```
The lsblk command - list information about block devices - like physical devices.  Type disk refers to the entire physical disk and partition refers to partion carved out of the disk.  MAJ:MIN - major min number of device driver 

RHEL example running as a virtual machine on vSphere:
```
$ lsblk
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda             8:0    0  100G  0 disk 
├─sda1          8:1    0  600M  0 part /boot/efi
├─sda2          8:2    0    1G  0 part /boot
└─sda3          8:3    0 98.4G  0 part 
  ├─rhel-root 253:0    0 63.5G  0 lvm  /
  ├─rhel-swap 253:1    0    4G  0 lvm  [SWAP]
  └─rhel-home 253:2    0   31G  0 lvm  /home
sr0            11:0    1 10.2G  0 rom  
```
- Debian example running on a Raspberry PI with a 32GB SDXC card for its drive
```
$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
mmcblk0     179:0    0 29.8G  0 disk 
├─mmcblk0p1 179:1    0  2.2G  0 part 
├─mmcblk0p2 179:2    0    1K  0 part 
├─mmcblk0p5 179:5    0   32M  0 part 
├─mmcblk0p6 179:6    0  256M  0 part /boot
└─mmcblk0p7 179:7    0 27.3G  0 part /
```
- mmcblk0 is physical disk - p1 through p5 are resuable partitions created for the disk
- Major:Minor - major number with associated device defines associated device drive, minor number differntiates between associated partsions
	
The lscpu utility- CPU architecture - 32-bit or 64-bit processors, number cores, type of processor, etc.

RHEL example running as a virtual machine on vSphere:
```
 lscpu
Architecture:        x86_64
CPU op-mode(s):      32-bit, 64-bit
Byte Order:          Little Endian
CPU(s):              2
On-line CPU(s) list: 0,1
Thread(s) per core:  1
Core(s) per socket:  1
Socket(s):           2
NUMA node(s):        1
Vendor ID:           AuthenticAMD
CPU family:          23
Model:               8
Model name:          AMD Ryzen 7 2700X Eight-Core Processor
Stepping:            2
CPU MHz:             3792.874
BogoMIPS:            7585.74
Hypervisor vendor:   VMware
Virtualization type: full
L1d cache:           32K
L1i cache:           64K
L2 cache:            512K
L3 cache:            16384K
NUMA node0 CPU(s):   0,1
Flags:               ...
```
- Debian example

```
$ lscpu
Architecture:        armv7l
Byte Order:          Little Endian
CPU(s):              4
On-line CPU(s) list: 0-3
Thread(s) per core:  1
Core(s) per socket:  4
Socket(s):           1
Vendor ID:           ARM
Model:               3
Model name:          Cortex-A72
Stepping:            r0p3
CPU max MHz:         1500.0000
CPU min MHz:         600.0000
BogoMIPS:            108.00
Flags:               half thumb fastmult vfp edsp neon vfpv3 tls vfpv4 idiva idivt vfpd32 lpae evtstrm crc32
```

Total number of threads availabel is equal to sockets X Cores X thrads

The lsmem command outpus a summer of memory --summary  list available memory on the system
RHEL example:
```
$ lsmem
RANGE                                  SIZE  STATE REMOVABLE BLOCK
0x0000000000000000-0x000000001fffffff  512M online        no   0-3
0x0000000020000000-0x0000000027ffffff  128M online       yes     4
0x0000000028000000-0x000000006fffffff  1.1G online        no  5-13
0x0000000070000000-0x00000000afffffff    1G online       yes 14-21
0x00000000b0000000-0x00000000bfffffff  256M online        no 22-23
0x0000000100000000-0x000000013fffffff    1G online        no 32-39

Memory block size:       128M
Total online memory:       4G
Total offline memory:      0B
```
```
$ lsmem --summary
Memory block size:       128M
Total online memory:       4G
Total offline memory:      0B
```
- Debian example
```
$ lsmem
lsmem: This system does not support memory blocks
```

The free -m utilitys list free memory -m megabyte, -k kilobbyte, -g gb

RHEL example
```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:           3735         857        1539          26        1338        2569
Swap:          4055           0        4055
```

- Debian example
```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:           3838         152        3004          35         681        3519
Swap:            99           0          99

```

Utliity lshw - extract detail information on the hardware

### sudo

Run command as root - with sudo you can determine which commands a user can run as super user and also see a list of commands the user has run as root
```
$ sudo lshw
```
With sudo you can control who can run commands as root, which commands/programs that can be run, and replay commands the user has run as sudo

## Linux Boot Sequence
Four stages
  -   BIOS Post
  -   Boot Loader - GRUB2
  -   Kernel Initialization
  -   INIT process (service initalization using systemd)

There are two Ways to Start a linux device in stopped or halted state or reboot running system

BIOS POST has nothing to do with Linux. This is the h/w "boot" or start up process.  The power on self test (POST - make sure all devices attached the systme can start (checks all h/w). If there is an problem, then system will not proceed to the boot stage.    

After a successful POST the BIOS loads and executes the boot code which is located in the first sector of harddrive.  In Linux the boot code is typically located in /boot file system.  It loads the boot process with the boot screen tha has optional sections.  It then loads the kernel code into memory and hands over control to the kermel
	- Grand Unified Boot Load (GRUB 2) - primary boot loader for most Linux distros currently
	- Kernel is decompressed after loading. Kernel is in a compressed space to save memory.  The kernel then initalized the h/w and "sets up" memory - the kernel is loaded into memory
  	-  After the kernel is loaded it looks for an init process to setup the user space
	- Then the kernel looks for an INIT process and starts systemd.  It's responsible in bringing the system to a ready state.
  	-   systemd start system services, mounts drives, etc.  (System V or sysV init was the old services system).  Systemd reduces start up time since it runs services start up in parallel

To see what init process is using run:
- RHEL example
```
$ ls -l /sbin/init
lrwxrwxrwx. 1 root root 22 Jan 25 03:35 /sbin/init -> ../lib/systemd/systemd
```
- Debian example
```
$ ls -l /sbin/init
lrwxrwxrwx 1 root root 20 Aug  6  2021 /sbin/init -> /lib/systemd/systemd
```


### Run Levels
Linux can run in multiple modes set by the run level like the graphical mode.  These are call run levels.  Type the following to see the run level
- RHEL example with GUI enabled
```
$ runlevel
N 5
```

- Run levels include:
	- Runlevel 5 is the graphical mode
	- Runlevel 3 is the command line

- During boot the init process checks the runlevel to make sure all systems start to run in the correct mode.  In the Graphical mode, the graphics service are started and running

In newer Linux distros systemd is used as the init process.  Systemd runlevels are called systemd targets.  Run level 3 and 5 are the most commonly used run target levels

Runlevel | System Targets | Function
---------|----------------|----------
3 | graphical.target | Boots into a Graphical interface
5 | multiuser.target | Boots into a Command Line Interface

	
To see systemd target type:
```
$ systemctl get-default
graphical.target
$ ls -ltr /etc/systemd/system/default.target
lrwxrwxrwx 1 root root 36 Feb 13  2020 /etc/systemd/system/default.target -> /lib/systemd/system/graphical.target
```
The command 'systemctl get-default' looks up the default.target file located in /etc/systemd/system/default.target.  You can see this has symbolic link

Change systemd target (this example changes the run level from 5 to 3):
```
$ sudo systemctl set-default multi-user.target
```
The term runlevels is used in the sysV init systems. These have been replaced by systemd targets in systemd based systems.

sysV Runlevels | systemd Targets
---------------|----------------
runlevel 0 | poweroff.target
runlevel 1 | rescue.target
runlevel 2 | multi-user.target
runlevel 3 | multi-user.target
runlevel 4 | multi-user.target
runlevel 5 | graphical.target
runlevel 6 | reboot.target
	

### File types
"Everything is a file in Linux" - every object in linux is a "type" of file
Three types of files
- regular files - most common files containing text, images, scripts, configuration, shell scripts, jpeg
- directory - stores other directories and files
- special files (5 types_
  -  Character files - represent devices under /dev - mouse, keyboard
  -  Block files - under /dev - a block device reads to from and writes to a device in chunks of block either memory or hard disk
  -  Links: Links to data to Hard links and symbolic links (like a short cut or alias and are independent).   
  -  Sockets - communication between two processes
  -  Named pipes - speciaal process connection data between processes Data can flow bi-directionly


Identify file type
```
$ file /home/pslucas
/home/pslucas: directory
$ file /home/pslucas/text.txt 
/home/pslucas/text.txt: ASCII text
```
or use the list command 'ls' command to identify file type by the output
```
$ ls -ld /home/pslucas/
drwxr-xr-x 7 pslucas pslucas 4096 Apr 25 19:53 /home/pslucas/
$ ls -ld text.txt 
-rw-r--r-- 1 pslucas pslucas 34 Apr 25 19:54 text.txt
```
File Type | Identifier
----------|-----------
Directory | d
Regular File | -
Character Device | c
Link | l
Socket File | s
Pipe | p
Block Device | b
	
	
Identified by first letter d - directory, s - socket, b - block device, l link, p - pip - - regular file


### Filesystem Hierarchy
**/** - root partition   
**/bin** - (binary) basic programs and binaries date, cp, etc commands   
**/boot**   
**/dev** - (device) /dev file system contains special block and character device files.  Contains devices external hard disks, mouse and keyboard.  For example you will see a USB drive mounted under media with a /dev path.    
**/etc** - (editable-text configurations) stores most configuration files   
**/home**  - home directory contains all home directories for users except the root user.  The root user's home direct is located at /root       
**/lib** - (LibraryO and /lib64 shared libraries imported in to programs   
**/media** - USB drive mounted under media all external media is mounted under media file systems   
**/mnt** -  (mount as in mounting file systesm) used to temporariy mount file systems to copy files or access other drives   
**/opt** - (optional as in optional add-on package) install any 3rd part apps    
**/tmp** - (temporary) used to store temporary data    
**/usr** - (user) old systems kept home directory,  Now user space programs are kept here.  Or user land programs lke thunderbird email, and browser programs    
**/var** - (variable - holds variable data - some 3rd party apps maybe installed here) system writes logs and cached data to var    

Use this command to see all mounted file systesm
```
 $ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        27G   18G  8.1G  69% /
devtmpfs        1.8G     0  1.8G   0% /dev
tmpfs           1.9G   16M  1.9G   1% /dev/shm
tmpfs           1.9G  217M  1.7G  12% /run
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/mmcblk0p6  253M   49M  204M  20% /boot
tmpfs           384M     0  384M   0% /run/user/999
tmpfs           384M  4.0K  384M   1% /run/user/1000
tmpfs           384M     0  384M   0% /run/user/1001
```
- From RHEL 8.4
```
$ df -h
Filesystem             Size  Used Avail Use% Mounted on
devtmpfs               1.8G     0  1.8G   0% /dev
tmpfs                  1.9G     0  1.9G   0% /dev/shm
tmpfs                  1.9G   18M  1.9G   1% /run
tmpfs                  1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/mapper/rhel-root   64G  5.4G   59G   9% /
/dev/sda2             1014M  339M  676M  34% /boot
/dev/mapper/rhel-home   31G  268M   31G   1% /home
/dev/sda1              599M  5.8M  594M   1% /boot/efi
tmpfs                  374M   12K  374M   1% /run/user/42
tmpfs                  374M  4.0K  374M   1% /run/user/1000
```	
