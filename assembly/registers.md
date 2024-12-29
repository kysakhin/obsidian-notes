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