there are 2 types of registers classified based on if the application can modify them. 

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
# multipurpose registers

1. **RAX:** (accumulator) used for instructions such as multiplication, division.
2. **RBX:** (base index) holds the offset address of a location
3. **RCX:** (count) holds count
4. **RDX:** (data) can hold data or even memory address
5. RBP: (base pointer) 
6. RDI: (destination index) 
7. RSI: (source index) 
8. R8 through R15 (general purpose registers) can be used as as extended registers and for anything

## special purpose registers
1. RIP (instruction pointer) it holds the address of the next set of instruction. the register is IP (16 bits) when the processor is in real mode. and EIP (32 bits) when it is in protected mode. this can be modified by jump calls. 
2. RSP (stack pointer) pointer to the top of the stack memory. 
3. RFLAGS register holds the status of the processor. tracks the state, the arithmetic, logical operations. 

### RFLAGS
![](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fgrandidierite.github.io%2Fassets%2Fimg%2Feflags.png&f=1&nofb=1&ipt=4661c5bd2036c042140ca82f66af4d16f183e620d4ac14040542ba30f0b1ef80&ipo=images)

1. Carry flag: holds carry after addition or borrow during subtraction. can sometimes also indicate error conditions
2. Parity flag: is set if the LS BYTE has an even number of zeroes.
3. Auxiliary flag: indicates a carry/borrow between lower nibbles.
4. Zero flag: is set if the result becomes 0
5. Sign flag: is set if the result is negative
6. Trap flag: when this flag is set, the cpu generates a special interrupt (debug exception) after every interrupt
7. Direction flag: this is only how strings are iterated. if DF is 0 then process strings from low memory to higher memory. if it is set then pointer needs to be decreemented from higher memory to lower.
8. Overflow flag: occurs when signed numbers are added or subtracted and it has exceeded the capacity of the machine. for unsigned operations the overflow flag is ignored. for unsigned, the CF flag handles overflow conditions. 
9. I/O privilege level: is used in protected mode operation to select the privilege level for I/O devices. it uses 2 bits. so an IOPL of 00 is the highest and 11 is the lowest. if the IOPL is lower than the current privilege level then it suspends execution
10. Nested task flag: is set if the current task is nested within another task execution. this would mean that a context switch has occurred and that the cpu must store the status of the previous task
11. Resume flag: is used in debugging to control the resumption of execution after the next instruction. it works with the trap flag
12. Virtual Mode flag: to switch to virtual mode to simulate multiprocessing 
13. Alignment Check flag: checks if a word or doubleword is addressed on a non-word or a non-doubleword boundary. 

should be the important registers. 


## segment registers
are additional registers that generate memory addresses when combined with other registers. it functions differently in real mode and protected mode. these are somewhat like address pointers that help the cpu access different regions/segments of the memory. 

1. CS (code) holds the program code. 
2. DS (data) holds global variables and static variables are stored here
3. SS (stack) holds function calls and local variables. this also can be used to manage deallocation of variables after their scope is done. 
4. ES (extra) additional data segment used by some string instructions to hold data

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

segment value is shortened by 16\*\*1.

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

### global descriptor table
main table that stores descriptors. its always available to all the processes in the system. it typically contains:
- null descriptor
- code segments for the operating system
- data segments for the operating system
- task state segments for task switching

