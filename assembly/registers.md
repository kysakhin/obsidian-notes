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