# scheduler related system calls
relating back to the previous chapter, linux provides a family of system calls for the management of scheduler parameters. like

|       system call        |             description             |
| :----------------------: | :---------------------------------: |
|          nice()          |     sets a process's nice value     |
|   sched_setscheduler()   | sets a process's scheduling policy  |
|   sched_getscheduler()   | gets a process's scheduling policy  |
|     sched_setparam()     | sets a process's real time priority |
|     sched_getparam()     | gets a process's real time priority |
| sched_get_priority_max() | gets the highest real time priority |
| sched_get_priority_min() | gets the lowest real time priority  |
| sched_rr_get_interval()  |  gets a process's timeslice value   |
|   sched_setaffinity()    | sets a process's processor affinity |
|   sched_getaffinity()    | gets a process's processor affinity |
|      sched_yield()       |  temporarily yields the processor   |

# introduction
system calls are a set of interfaces by which processes running in user-space can interact with the system. \
![kernel protection ring](https://i.sstatic.net/DzNLZ.png) 

system calls provide a layer between hardware and user-space processes. this layer has 3 main purposes:
- acting as an abstracted hardware interface for user applications. when reading or writing a file, the application does not need to worry about the type of disk, media or filesystem type.
- security. since it prevents unauthorized use of hardware
- single common point for user space applications and rest of the system. 


# apis posix and the C library
api is a communicator between different software components. an api defines a set of programming interfaces that can be used as system calls. \
posix is a family of standards provided by the IEEE that aim to provide a portable operating system standard roughly based on unix. posix was a standard to resemble the interfaces provided by the earlier unix systems. linux tries to be posix complaint wherever possible. \
the system call interface in linux, similarly to most unix systems, is provided by the C library. the C library implements the main api on unix systems including standard C and system call interface. \
from the programmer's point of view, system calls aren't necessary. only apis. apis wrap around system calls for convenience and portability. on the other hand, kernel is only concerned about system calls. 

## system call numbers
each syscall is given a unique number. when a user space program executes a system call then it calls it with the number and not the actual name of the syscall. once the number is assigned, it cannot be changed, cannot be reused for some other syscall. the kernel keeps a list of all the registered system calls in the system call table. \
### performance
syscalls in linux are faster than in many other operating systems partly because of its really fast context switch times. entering and exiting the kernel is simple and streamlined. another factor is the simplicity of the system call handler and the syscalls themselves. 

# system call handler
before moving to this, some prerequisites to know is
### interrupt vector table 
this is like a phonebook of all the interrupts with their associated number, which points to an address in memory for handling that interrupt. the ivt table is typically located at 0x0000. for example, a real time example, 
1. you press a key
2. interrupt is now sent to the cpu to inform that a key was pressed
3. now cpu looks up to that interrupt number, maybe something like 0x18
4. then the ivt points to the handler of that interrupt to be at some address 0x1234 which points to the code that handles key presses
5. then the cpu runs that interrupt handler

## going back
since applications cannot directly execute kernel code because kernel resides in protected memory, applications signal the kernel using an interrupt. \
really really long ago, processors used the interrupt number 0x80 for system calls. this triggers a switch to kernel mode and handler is called system_call(). it is architecture dependent. \
but a more better way x86 processors handle is sysenter. 

## system calls wrappers
like for example, \
printf in C is essentially a wrapper for the syscall of std output (1). its just a lot better and has formatting and buffering to write std output. 

### denoting the correct system call
if you have even the slightest experience in assembly, we basically denote registers to values or pointers. 
- rax register for syscall id (viz. write)
- rdi register for file descriptor (viz. stdout, stdin, or stderr)
- rsi points to the address of the string or data to write
- rdx is the length of the string.
something like this, 
```asm
mov rax, 1
mov rdi, 1
mov rsi, msg
mov rdx, (some number)
syscall
```
so before entering the kernel-space the syscall number is fed into the rax register. the user-space sticks in the syscall associated to a given syscall and then the system call handler reads the value from rax.

## parameter passing
just like how it works in assembly, the user-space must relay these parameters down to the kernel. for example, \
![some syscalls with their parameters](https://blog-pictures.vercel.app/syscalltable.png)


# writing your own system calls??? 
please test this in a safe environment. a virtual machine will be more preferred. \
**this can be dangerous** \

## some important considerations
1. choose a unique syscall number that doesnt conflict with the existing ones
2. ensure proper error handling
3. follow kernel coding standards
4. be careful with kernel memory access
5. test in a safe environment

# steps
for this im using arch linux cli version. \
since arch installation on is pretty confusing for a few, here's a premade virtual disk image [Arch Linux Disk Image](https://www.osboxes.org/arch-linux/#archlinux-20240601-vbox)
the actual directory structure and file locations will be different for different OSes \

### **dependencies**
make sure you have all the dependencies
```bash
# update mirrors (not necessary. just makes installation faster)
sudo pacman -S reflector
sudo reflector --verbose --latest 10 --country 'India' --sort rate --save /etc/pacman.d/mirrorlist

# update system
sudo pacman -Syu

# install dependencies
sudo pacman -S base-devel bc cpio xmlto docbook-xsl kmod inetutils git python libelf pahole

```
## writing the syscall
then **clone the repository** wherever you want to
```shell
git clone --depth=1 https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
cd linux
```


1. cd into `kernel/` and create your new syscall.
```
cd kernel/

vim hello_syscall.c
```

2. write some syscall logic. here's a very simple one. remember that kernel doesnt use standard C library. 
```C
#include <linux/syscalls.h> 

SYSCALL_DEFINE0(hello_world) { 
	printk(KERN_INFO "Hello from kernel space!\n"); 
	return 0; 
}
```
`SYSCALL_DEFINEN` is a family of macros that make it easy to define a system call with N arguments.


3. add your syscall number to your table. on my system, it was like this. NOTE THAT SYSCALL NUMBER WILL DIFFER. 
	* **File: arch/x86/entry/syscalls/syscall_64.tbl**
```
548 common hello_world sys_hello_world

# 548 is the system call number (MAY DIFFER FOR YOU. use the next number)
# hello_world is the name that you've defined in the previous step
```

4. add system call prototype
in the file `include/linux/syscalls.h` be sure to add this line before the `#endif`
```
asmlinkage long sys_hello_world(void);
```

5. after that in your `kernel/Makefile` add this line
```
obj-y += hello_syscall.o
```
this should be the same as the .c filename that you have created in the first step.

6. copy the default config file from your existing system. this gives a pretty good configuration files 

```
cp /boot/config-$(uname -r) .config

# or

zcat /proc/config.gz > .config
```

from here, you can either leave it as it is, or \
you can do 
```
make olddefconfig
```
this will update the current config files to the new options. 


## building the kernel
this will take a lot of time. so if you have a bad processor or have configured your vm to use less ram or processor, be prepared for your vm to crash or have problems. \
i would suggest you to use **qemu** since it supports kernel virtualization and is offers some really great performance benefits. \
if you do end up using qemu, then the command to run it is: 

```bash
qemu-system-x86_64 \
    -hda path/to/.vdi \   # Path to virtual disk image (VDI format)
    -m 4G \              # Allocate 4 gigabytes of RAM to the VM
    -smp 4 \             # Use 4 CPU cores (symmetric multiprocessing)
    -enable-kvm \        # Enable KVM hardware virtualization for better performance
    -vga std            # Use standard VGA card emulation
```

1. make 
	this is gonna take a really long time. sometimes even 1hr+ if there are no errors.
```bash
make -j$(nproc)
make modules_install
```

2. copying the bzImage and building the initramfs. 
	to make things easier, here's a small script: 

```bash
#!/usr/bin/bash

# change this if you want 
SUFFIX="-axolo"

# This causes the script to exit if an error occurs
set -e

# Install kernel image
cp arch/x86_64/boot/bzImage /boot/vmlinuz-linux$SUFFIX

# Create preset and build initramfs
sed s/linux/linux$SUFFIX/g \
    </etc/mkinitcpio.d/linux.preset \
    >/etc/mkinitcpio.d/linux$SUFFIX.preset

# this command is arch specific. for other distros please refer to "how to create a initial ramdisk environment on (your distro)"
# examples for other distros:
# Ubuntu/Debian: update-initramfs -c -k <kernel_version>
# Fedora/RHEL: dracut --force --kver <kernel_version>
mkinitcpio -p linux$SUFFIX

# Update bootloader entries with new kernels.
grub-mkconfig -o /boot/grub/grub.cfg

```


# testing if everything is working...
1. boot into your new kernel. \
in the grub menu, go to \
**Advanced options for Arch Linux**
![](https://blog-pictures.vercel.app/syscall1.png)

2. select your new kernel \
![](https://blog-pictures.vercel.app/syscall2.png)

3. if everything went correctly then you can login using credentials
4. there are 2 ways to test if its working. using C and another is using Assembly. choose whichever you are comfortable with.


## using C
1. create a new .c file and type these out. \
here, the 548 was the system call number i had put in before. during configuration.

![](https://blog-pictures.vercel.app/syscall3.png)

2. compile and run it \
if it returns a status of 0, then congrats :D everything went correctly.
![](https://blog-pictures.vercel.app/syscall4.png)

3. check dmessages \
![](https://blog-pictures.vercel.app/syscall5.png)


you can see that it says "Hello from kernel space!"


## in assembly
i will be using the nasm and assembling with respect to x86_64. \
1. create a new .asm file and add the following code \
![](https://blog-pictures.vercel.app/syscall6.png)

2. assemble it, link it and then run it. \
![](https://blog-pictures.vercel.app/syscall7.png)


it says "Hello from kernel space!" twice here. 