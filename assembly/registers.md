### there are 2 types of registers classified based on if the application can modify them. 

## program visible registers
1. general purpose registers. RAX, RBX RCX etc
2. segment registers. CS DS ES SS FS GS
3. instruction pointer. the pointer for the next line of instruction.
4. control registers
5. debug registers
6. flag registers

## program invisible registers
1. segment descriptor registers
2. internal registers

---

## terms to understand
registers come in varying sizes. ranging from 16 bits to 64 bits. \
![](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fdz2cdn1.dzone.com%2Fstorage%2Ftemp%2F14016768-register.png&f=1&nofb=1&ipt=c6f2278aae1009ea0049d5cf7d317ad50ed9996eef71f270289607da89eb50d8&ipo=images)

the 'R' signifies 64 bit. 'E' specifies 32 bit. \
this convention works ONLY for registers from R0-R7. they have different names, as discussed later. \
in some places i only mention the AX to specify a register.

---
# general purpose registers
also called as multipurpose registers

1. **RAX:** (accumulator) used for instructions such as multiplication, division.
2. **RBX:** (base index) holds the offset address of a location
3. **RCX:** (count) holds count typically during loops
4. **RDX:** (data) can hold data or even memory address
5. **RBP**: (base pointer) used to point to the stack frame in function calls
6. **RDI**: (destination index) for string and memory operations
7. **RSI**: (source index) for string and memory operations
8. **RSP**: (stack pointer) points to the top of the stack.
9. R8 through R15 (general purpose registers) can be used as as extended registers and for anything

registers from R8 to R15 have a different hierarchy for their smaller 32-bit and 16-bit counterparts. \
- **R8–R15**: full 64-bit registers.
- **R8D–R15D**: lower 32 bits (D = double word).
- **R8W–R15W**: lower 16 bits (W = word).
- **R8B–R15B**: lower 8 bits (B = byte).

## special purpose registers
1. RIP (instruction pointer) it holds the address of the next set of instruction. the register is IP (16 bits) when the processor is in real mode. and EIP (32 bits) when it is in protected mode. this can be modified by jump calls and automatically while executing instructions one by one
2. RSP (stack pointer) pointer to the top of the stack memory. 
3. RFLAGS register holds the status of the processor. tracks the state, the arithmetic, logical operations. 

### RFLAGS (64-bit) / EFLAGS (32-bit)
![](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fgrandidierite.github.io%2Fassets%2Fimg%2Feflags.png&f=1&nofb=1&ipt=4661c5bd2036c042140ca82f66af4d16f183e620d4ac14040542ba30f0b1ef80&ipo=images)

1. Carry flag: holds carry after addition or borrow during subtraction. can sometimes also indicate error conditions
2. Parity flag: is set if the LS BYTE has an even number of zeroes.
3. Auxiliary flag: indicates a carry/borrow between lower nibbles (4 bits) of a byte during arithmetic operations.
4. Zero flag: is set if the operation is 0
5. Sign flag: is set if the operation is negative (based on the MSB. the MSB holds the sign. 1 for negative and 0 for positive)
6. Trap flag: when this flag is set, the cpu generates a special interrupt (debug exception) after every instruction
7. Direction flag: this is only how strings are iterated. if DF is 0 then process strings from low memory to higher memory. if it is set then pointer needs to be decreemented from higher memory to lower.
8. Overflow flag: occurs when signed numbers are added or subtracted and it has exceeded the capacity of the machine. for unsigned operations the overflow flag is ignored. for unsigned, the CF flag handles overflow conditions. 
9. I/O privilege level: is used in protected mode operation to select the privilege level for I/O devices. it uses 2 bits. so an IOPL of 00 is the highest and 11 is the lowest. if the IOPL is lower than the current privilege level then it suspends execution
10. Nested task flag: is set if the current task is nested within another task execution. this would mean that a context switch has occurred and that the cpu must store the status of the previous task
11. Resume flag: is used in debugging to control the resumption of execution after the next instruction. it works with the trap flag
12. Virtual Mode flag: to switch to virtual mode to simulate multiprocessing 
13. Alignment Check flag: checks if a word or doubleword is addressed on a non-word or a non-doubleword boundary. 

should be the important registers. 


## segment registers
segment registers are used to divide memory into segments and help the cpu generate addresses in conjunction with other registers, especially in real mode and protected mode. they help access different regions by combining with offsets.

1. CS (code) holds the program code. 
2. DS (data) holds global variables and static variables are stored here
3. SS (stack) holds function calls and local variables. this also can be used to manage deallocation of variables after their scope is done. and also the stack grows to lower memory addresses. unlike convention when the top of the stack is incremented after a push, here it is decremented. 
4. ES (extra) additional data segment used by some string instructions to hold data
5. FS and GS are extra segments thats used for special purposes like thread specific data in some operating systems.

# real mode memory addressing

real mode is a legacy operating mode that some of the older x86 processors used. it was the default mode until newer modes were introduced. \
in real mode the cpu can only access the first 1 MB of memory. and it also has no memory protection thats seen in modern day protected mode, like protection rings.

### segments and offsets
in real mode memory is addressed in a segmented memory model. memory is divided into different segments each of 64 KB. and segments are accessed using 16-bit memory addresses. the processor uses the segment registers (CS, DS, SS, ES) to point to different segments of memory. \
then offset addresses selects any given address in that given segment. 

segments in real mode always have a size of 64 KB. so for example
if a segment starts at 0x00000, then it ends at 0xFFFFF. then the next segment starts at 0x10000 and ends at 1FFFFF. 

real address is calculated by \
```
$$
physical address = (segment value * 16) + offset
$$
```
for example, if there exists a segment register of value 0x1234, then the physical memory location would be 0x12340. \
this is hexadecimal notation. \
another example: for a segment register: 0xAB00, then its physical address is 0xAB000. \
the bits are left shifted once. 

now coming to offsets, its calculated by a simple hexadecimal addition. \
![](https://blog-pictures.vercel.app/segment_offset.png)

# how it all adds up:

## CS register
- points to the segment containing the currently operating instruction
- used with IP (instruction pointer)
- when executing an instruction, the physical address is calculated by CS:IP (segment register):(offset)
- cannot be directly modified. only by jump and call instructions

## DS register
- used for general data access
- works with AX, BX, CX, DX, SI, DI registers 
- can be modified using mov instruction

## SS register
- points to segment containing stack
- offsets are the SP(stack pointer) or BP(base pointer) registers.
- SS:SP points to the top of the stack
- interrupts between SS and SP modifying can sometimes crash the system


![](https://external-content.duckduckgo.com/iu/?u=http%3A%2F%2Fece-research.unm.edu%2Fjimp%2F310%2Fslides%2Fmicro_arch1-4.gif&f=1&nofb=1&ipt=3f2500132f057d640de15cb3d242aafdd29bbcb83d1683a19800bc164d5027f8&ipo=images)


the segment:offset scheme allows for relocation. it also allows programs written to function in real mode to operate in protected mode aswell. once it is relocated, it can still be executed without change or moving back to initial place. 


# protected mode memory addressing 
protected mode memory addressing allows access to data located above the first 1MB of memory aswell as the first 1MB. 

offset address is still used here aswell but no segment address is no longer present. in place of the segment address, the segment register contains a selector that selects descriptor from the descriptor table. this is not to be confused with the process descriptor table that exists on oses. descriptor table here is purely hardware-level structure. the descriptor describes the memory segment's location, length and access rights. 

unlike real mode, protected mode doesnt access physical memory directly. instead it uses a layer of abstraction through **descriptors.**

some differences between real mode and protected mode

| **Feature**           | **Real Mode**                              | **Protected Mode**                                  |
| --------------------- | ------------------------------------------ | --------------------------------------------------- |
| **Address Space**     | Limited to **1 MB** (20-bit addressing).   | Can access up to **4 GB** (32-bit addressing).      |
| **Memory Addressing** | Uses a simple segment:offset scheme.       | Uses segment selectors with descriptors in a table. |
| **Protection**        | No memory protection.                      | Provides memory protection and isolation.           |
| **Multitasking**      | Not supported.                             | Fully supported.                                    |
| **Paging**            | Not available.                             | Supports paging for virtual memory.                 |
| **Interrupts**        | Uses the real mode interrupt vector table. | Uses an interrupt descriptor table (IDT).           |


## selectors and descriptors
there are two descriptor tables used with the segment registers. one contains global descriptors and the other contains local descriptors. a descriptor is a detailed label that tells the cpu what kind of segment it is. like: 
1. base address
2. segment limit
3. access rights
4. type of segment (code, data, system)
5. various control flags

each descriptor is 8 bytes (64 bits long). its classified somewhat like this
```
base (32 bits total, split into 3 parts)
limit (20 bits total, split into 2 parts)
access rights and flags (12 bits)
```
# program invisible registers

all descriptor tables are invisible registers
1. global descriptor table register (GDTR) is a table that contains descriptors for all the global segments. 
2. interrupt descriptor table register (IDTR) is a table that contains all the interrupt vectors
3. local descriptor table register (LDTR) is a table used to store segment descriptors specific to a particular task or process. 

each of the segment registers contains a program-invisible portion used in the protected mode. this portion of these registers are also called as cache memory. this is not the same as L1, L2 cache memory. the program-invisible portion of the segment register is loaded with the base address, limit, and access rights each time the number segment register is changed. when a new segment number is placed in the segment register, the microprocessor accesses a descriptor table and loads the descriptor into the program-invisible registers. 

these program invisible registers are essentially helper registers for the previously discussed segment registers, the CS, DS, SS etc. 

# memory paging
it is a memory management scheme that divides contiguous memory locations into smaller "pages". terms related are:
1. page: is a fixed size block of memory in virtual memory. 
2. page frame: is a physical block of memory in ram.
3. page table: is a data structure used by the os to map virtual pages to physical page tables in ram.
4. page fault: occurs when a program tries to access a page that is not in physical memory. 

### the paging process
1. the program generates a virtual address, lets say 0x12345
2. the os uses the page table to convert it into physical address
3. if the page containing that address isnt currently in physical memory, then a page fault occurs and os loads the required page into memory
4. sometimes also can swap with an existing page

## paging registers
the paging unit is controlled by these paging registers. \
![](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fimg-blog.csdn.net%2F20160223204421454&f=1&nofb=1&ipt=9c3fb33e60829d8c5e333c341389accc1e9ca10a8b6bf5f71bb4396572489c22&ipo=images)

CR0 to CR4 are called control registers. the registers we are concerned about right now is only the CR0 and CR3

the leftmost bit (PG) position of CR0 selects paging when placed at a logic 1 level. if it is cleared, the virtual address generated by the program becomes the physical address used to access memory. if the PG is set, then the virtual address is converted to a physical address using the page table. 

in x86 and x86-64 architectures, CR3 is a control register that holds the physical address of the page directory in systems with paging enabled.

### page directory
a page directory is like an address book for other page tables. and page tables are used to convert virtual addresses to physical ones. so the CR3 base is gonna hold the physical address of the first page directory. some systems have multiple page directories. one page directory can cover 4 GB of memory. \
essentially, each entry in the page directory holds a physical address to a page table. and that page table contains entries to map the virtual address to physical memory locations.

![](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.2Fq6I1YwT8EZmzY4Cep5XAAAAA%26pid%3DApi&f=1&ipt=e8a06b47bbfce2569399014183bd43ecbf57d71638be71291d4d2ca79bdbae9f&ipo=images)


