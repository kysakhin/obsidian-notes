### `usr/src/linux`
directories under it

`arch` files related to architecture of the architecture used eg x86
`block` files related to block devices like hard drives. it also involves partitioning information. 
`crypto` cryptographic algorithms
`Documentation` like the word says
`drivers` ykw it is
`firmware` 
`include` header files, kernel data structures, macros 
`init` initialises low level stuff like fs mounting, device init
`ipc` interprocess communication
`kernel` process management, scheduling, interrupt handling, and various other low-level mechanisms 
`lib` helper routines that's gonna be used elsewhere
`mm` memory management
`net` networking (you dont say)
`scripts` scripts for development, building and maintenance
`security` security :| 
`usr` initramfs early user-space code
`virt` virtualization infra 
`sound` `samples` `tools`

## Configuring
configuration options can be bool or tristates. 
a tristate has options for y n and module. the mentioned module will be loaded at kernel runtime using insmod or modprobe. this is what gives linux kernel dynamic loading of module.
it is compiled beforehand into a separate kernel object with .ko extension.

### making config
using `make config` to do it from cli. usually very slow.
`make menuconfig` is an ncurses-based interface
`make gconfig` gtk+ based interface
`make xconfig` Qt based interface
all these options prompts the user for making config
`make defconfig` just makes a default config

after generation, it is stored in a `.config` file in the same directory.
super long dont try to cat it.

### building
`make` fetches details from `.config` and does stuff.
what stuff you may ask preprocessing, compiling, linking, creating modules and building initramfs
make -j(n) spawns multiple make jobs. usually 2 jobs per processor. 

## Installing
`sudo make modules_install` installs and copies built kernel modules to `/lib/modules/(kernel ver)` 
`sudo make install` copies the kernel image, system map file 

## Characteristics / Drawbacks
> Kernel has no access to C library nor the standard C lib
> It is coded in GNU C
> can access user-space memory  
> kernel c library does not have that great floating point operations. 
> it uses a different C library since the standard C library isn't very efficient and bulky. 
> the kernel must be able to handle interrupts efficiently.

## Some assembly basics
1. `mnemonics` are like keywords. 
2. `registers` are fast storage locations within CPU
3. `operands` values or addresses used by instructions
4. `labels` label for symbolic names in the assembly code. they are used to mark points and jump to them.
5. `directives` are special instructions to the assembler. they just help setting up the code. tells it how to process the given assembly code.
why is it important? because the gcc compiler can handle assembly files and can output a C/C++ program into assembly code
#### gcc and assembly language
gcc supports inline assembly. viz. directly adding assembly code onto the .c file.

## some more cool stuff
anyone can write header files ofc
header files are mostly only used for declaration of C items like functions, macros, constants and type definitions.
`#include "header.h"` at the beginning of the file accesses the header file and can be defined in that .c file.
yeah you can define all those stuff on your .c file aswell. the only benefit is that you can use it over multiple files. even chatgpt told same. its just better encapsulation and can potentially only boost compilation time that's it. not runtime.

directives: directives in assembly are instructions directly to the assembler. like defining data, reserving space. they guide the assembler on how to assemble the code.

> inline functions

so inline functions are kinda cool. instead of performing function call to a regular function or a return variable, it just replaces that part where it is called upon by the block of code itself. this saves CPU cycles that would otherwise be needed to perform it. and also memory efficient since it would have to store the previous values in a register which wont be necessary in case of an inline function. only small functions can be made inline. since during compilation are basically copied into the line they are called upon. effectively increasing the size of the binary.

gcc has a built in directive (a directive is an instruction to the compiler on how to process the code) that can optimize conditional branches based on likely and unlikely. and makes the likely case to be more efficient. 

the reason kernel space does not make use of floating point numbers is because unlike integers which are stored in base-2 format, zeroes and ones, a floating point needs data for:
  sign: pos or neg
  exponent: describes the scale of numbers.
  mantissa: contains significant digits of the number.
