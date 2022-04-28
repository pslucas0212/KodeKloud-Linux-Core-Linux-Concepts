# KodeKloud Linux Basics: Linux Core Concepts

[KodeKloud Linux Basics Course Notes Table of Contents](https://github.com/pslucas0212/LinuxBasics)

### Linux Kernel
- The kernel is the interface between applciations/processes and the underlying h/w - CPU, Memory, Storage, I/O.  The kernel manages and schedules resources
- Kernel is responsible for 4 major tasks
  - Memory Mangement
  - Process managment - who can use the CPU when
  - Device Drivers 
  - Systems Calls and security
- Kernel is monolithic - carries out the work above by itself
- Kernel is modular - can extend its capability by loading dynamic extenxsions of the kernel																			
- Hardware <--- Kernel Space (([Device Drivers)([kernel)) <--- System calls ---- User Space (Applications/Programs)

- To see kernel name type
```
$ uname
Linux
```
- To see kernel version type 
```
$ uname -r
5.10.63-v7l+
```
- '<kernel version>.<Major version><Minor vers><patch release>' and may include distro specif information
	
	
- See the kernel.org webssite to get more information on the Linux kernel

#### Kernel and User Space
- Memory management is what of the most task of the kernel
- memory is divided into two spaces: Kernel space and user space
  - Kernel space conatins the kernel (code and extensions) and hardware device drives and a process running in the kernel space has unrestricted access to hardware and runs the Kernel code, kernel extensions and device drivers
  - User space contains application and programs - all process running outside kernel space which restricts access to h/w
    - user space applications - user space also called user land
    - Applications get access to data and code in memory and/or on disk by making system calls to the kernel (see following examples)
	- Opening a file 
	- write a file
	- list process
	- Allocating memory by defining a variable
    - examples: programs written in Java, C, Python
	

- Data for users stored in memory or disk.  Applications in the user space access data by making system calls to the kernel space, which in turn makes calls to device drivers to the underlying physical hardware


- system calls include open(), close()

#### Working Hardware
- When attaching a device like a USB disk to the system gerneraters a kernel event where the device driver for the USB disk is loaded into the kernel space.  The event is know as a uevent which is sent to the user device management system daemon called udev in the user space.  The udev services dynamically creates device node created on the device folder /dev/usb


- dmesg tool get messages from the ring buffer
```
$ dmesg | grep -i usb
```
- udevadm ia admin tool can query the udev database
	
```
$ udevadm info --query=path --name=/dev/ttyUSB0
/devices/platform/scb/fd500000.pcie/pci0000:00/0000:00:00.0/0000:01:00.0/usb1/1-1/1-1.3/1-1.3:1.0/ttyUSB0/tty/ttyUSB0
```

- udevadm monitor is used for monitoring udev events especially when attaching or removing devices
```
$ udevadm monitor
```

- lspci - list info about pci devices - like network cards, video cards, any device that attaches directly to the mother board via PCI.
```
$ lspci
00:00.0 PCI bridge: Broadcom Limited Device 2711 (rev 10)
01:00.0 USB controller: VIA Technologies, Inc. VL805 USB 3.0 Host Controller (rev 01)
```
- lsblk - list information about block devices - like physical devices.  Type disk refers to the entire disk and part refers to partion carved out of the disk.  MAJ:MIN - major min number of device driver 
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
	
- lscpu - CPU architecture - 32-bit or 64-bit processors, number cores, type of processor, etc.
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

- lsmem --summary - list available memory on the system
```
lsmem --summary
Memory block size:       128M
Total online memory:       4G
Total offline memory:      0B
```

- free -m - list free memory -m megabyte, -k kilobbyte, -g gb
```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:           3735         797        1823          18        1114        2670
Swap:          4055           0        4055
```

- lshw - extract detail information on the hardware

#### sudo

- run command as root - with sudo you can determine which commands a user can run as super user and also see a list of commands the user has run as root
```
$ sudo lshw
```
- With sudo you can control who can run commands as root, which commands/programs that can be run, and replay commands the user has run as sudo

## Linux Boot Sequence
- Four stages
  -   BIOS Post
  -   Boot Loader - GRUB2
  -   Kernel Initialization
  -   INIT process (service initalization using systemd)

There are two Ways to Start a linux device in stopped or halted state or reboot running system

- BIOS POST has nothing to do with Linux. The power on self test (POST - make sure all devices attached the systme can start (checks all h/w). If there is an problem, then system will not proceed to the boot stage.    
- After a successful POST the BIOS loads and executes the boot code which is located in the first sector of harddrive.  In Linux located in /boot file system.  It loads the boot process with the boot screen tha hats optional sections.  It then loads the kernel code into memory and hands over control to the kermel
	- Grand Unified Boot Load (GRUB 2) - primary boot loader for most Linux distros
	- Kernel is decompressed after loading. Kernels in a compressed space to save memory.  It then initialized h/w and setups memoty - the kernel is loaded into memory
  	-   After kernel is loaded it looks for an init process to setup user space
	- Then the kernel lookds for an INIT process starts systemd.  It's responsible in bringing the system to a ready state.
  	-   systemd start system services, mounts drives, etc.  (System V or sysV init was the old services system).  Systemd reduces start up time since it runs services start up in parallel

To see what init process is using run:
```
ls -l /sbin/init
lrwxrwxrwx 1 root root 20 Aug  6  2021 /sbin/init -> /lib/systemd/systemd
```


### Run Levels
Linux can run in multiple modes set by the run level like the graphical mode.  These are call run levels.  Type the following to see the level
```
$ runlevel
N 5
```
- Run levels include:
	- Runlevel 5 is the graphical mode
	- Runlevel 3 is the command line

- During boot the init process checks the runlevel to make sure all systems start to run in the correct mode.  In the Graphical mode graphics service smust run 

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
/ - root partition  
/bin - (binary) basic programs and binaries date, cp, etc commands  
/boot  
/dev - (device) /dev file system contains special block and character device files.  Contains devices external hard disks, mouse and keyboard.  For example you will see a USB drive mounted under media with a /dev path.
/etc - (editable-text configurations) stores most configuration files  
/home  - home directory contains all home directories for users except the root user.  The root user's home direct is located at /root
/lib - (LibraryO and /lib64 shared libraries imported in to programs  
/media - USB drive mounted under media all external media is mounted under media file systems 
/mnt -  (mount as in mounting file systesm) used to temporariy mount file systems to copy files or access other drives 
/opt - (optional as in optional add-on package) install any 3rd part apps  
/tmp - (temporary) used to store temporary data  
/usr - (user) old systems kept home directory,  Now user space programs are kept here.  Or user land programs lke thunderbird email, and browser programs  
/var - (variable - holds variable data - some 3rd party apps maybe installed here) system writes logs and cached data to var  

Use this command to see all mounted file systesm
```
 $ df -hP
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
	$ df -hP
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
