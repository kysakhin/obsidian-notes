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
![](https://i.sstatic.net/DzNLZ.png) 

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
![](https://blog-pictures.vercel.app/syscalltable.png)


