# introduction
addressing modes in assembly determine how the operands (data or addresses) are used for instructions. the common data addressing modes include, \
register, direct, register indirect, base-plus index, register-relative, and base relative-plus-index mode of addressing. 

## objectives
1. explain operation of each data addressing mode
2. use data addressing modes to form assembly language statements
3. select the appropriate addressing mode to accomplish a task
4. differences in addressing in real mode and protected mode
5. explain how a data structure is placed in memory and used with software
6. mov and jump calls


# data addressing modes
using the mov instruction, we can move data from a source to a destination register. \
```
mov destination, source
```
the data addressing modes are: 
1. register addressing: using the register name to move data from source to destination
2. immediate addressing: directly moving the value into registers. it can be a label, a number, decimal, hex or even binary or a character.

### immediate addressing mode
```
mov rax, 25
```
just straight up assigning rax the value of 25

### register addressing mode
```
mov rax, rbx
```
moving value stored in a register to another.

### direct addressing mode
```
mov rax, [0x1234]
```
move the value stored in location 0x1234 into rax

### indirect addressing mode
```
mov rax, [rbx]
```
here the rbx register is currently storing an address, like 0x1234. so by \[rbx] we are referring to the address inside that register. \
**in x86 assembly, when a register is used to store an address, then it stores only the offset value. segments and offsets are discussed in the previous chapter.**
so, **in real mode** it uses the CS:Offset scheme. \
if DS starts at address 0x1000, RBX has an offset of 0x0300, then \
(0x1000 x 16) + 0x0300 = 0x10300

### indexed addressing mode
```
mov rax, [rbx + rsi]
```
here the rbx and rsi are gonna hold offsets and they are gonna be added together.

### base-register addressing mode
```
mov rax, [rbx + 4]
```
giving direct offset instead from another register.

### base relative-plus-index addressing mode 
```
mov array[rbx+rsi], rdx
```
consider array to be at 0x1000. this is essentially, \
DS x 16 + array + rbx + rsi
this mode is commonly used for accessing multi-dimensional arrays or structured data with multiple indices

## variations of mov / extensions
### movzx (move with zero extension)
```
movzx ax, bl
```
since bl is 8-bit, and ax is 16-bit, we need to pad the extra bits. movzx will pad it with 0s. used for handling unsigned numbers

### movsx (move with sign extension)
```
movsx ax, bl
```
similar to movzx, this is used for signed integers. \
if you didn't already know, negative numbers are stored as their 2s compliment in binary in memory. storing a negative value in its 2s compliment allows for simpler hardware needed for computing. in this way addition and subtraction can be done with the same circuits. \
so going back, in signed numbers, the msb indicates the sign. 0 for positive and 1 for negative. if the msb is set, then while moving then the rest of the bits are also set. and if the msb is 0 then the rest fo the bits are also 0.

### cmov(cc) (conditional move)
```
cmp ax, bx ; compare values in registers ax and bx
cmovg cx, dx ; if ax > bx then move value in dx to cx
```
lets break this down \
cmp is an assembly instruction that compares 2 registers, and based on that it changes the flags in the eflags register. eflags/rflags was discussed in the previous chapter. \
based on that we can perform a jump operation to a label. label is just a marker in assembly code. almost like functions but this is not limited to braces that define scope of a function. code in assembly is linear. after you jump to a label, it continues from there till the end of the program. the instructions that it skips over will not be executed. so in assembly, the labels must also be properly arranged and structured. \
cmov is used in compiler design to reduce the need for branching

--- 
signed and unsigned integers use different flags for comparisons. 

## unsigned comparison
since they dont have negative values, comparison is simply done by subtraction. and from the result, the **Carry Fag (CF)** is set. \
if CF = 1, that means that a borrow occurred, meaning that the first number was lesser than the second. \
if CF = 0, that means that there was no borrow involved, and hence the second number was the lesser one. \
along with this, the Zero Flag (ZF) is also used to track equality

## signed comparison
since signed integers use their MSB for sign, we need to consider the **Sign Flag (SF)** and the **Overflow Flag (OF)** for results. \
if SF = 1 means that the result of the subtraction was negative. \
if OF = 1 means that a large negative number was subtracted from a positive number. \ 

example:
- AL = 0x7F (127 in decimal) and BL = 0x80 (-128 in decimal).
- after `cmp AL, BL`:
    - SF = 0 because the result (127 - (-128)) is positive.
    - OF = 1 because the result exceeds the range of a signed 8-bit integer.

---
# cmp
to understand the next topic, jumps, we need to understand cmp. \
cmp is for comparing two values \
```
cmp a, b
```
this subtracts b from a. (a-b) \
based on the result of this subtraction, certain flags are set/cleared. \
and jumps works on these flags. \
the eflags/rflags from previous chapter are used to store flags. they essentially capture the state of the operation.

---
# jumps
jumps are used to move the ip (instruction pointer) register to different regions
## unconditional jumps
```
jmp label
```
just jumps to a given label

## conditional jumps
when we perform any instructions for example, **cmp,** (compare) it performs a comparison between the operands. and based on the result, the flags in the eflags/rflags register, are modified. and based on the state of these flags, we can perform conditional jumps.

### *unsigned conditional jumps*
a **cmp** instruction subtracts the second operand from the first. \
cmp a, b is like a-b. and based on the result, bits in the eflags/rflags are updated.
based on the flag needed for checking, 
1. je/jz (jump if equal/jump if zero)
> jumps if the zero flag is set.

2. jne/jnz (jump if not equal/jump if not zero)
> jumps if zero flag is cleared

3. ja/jnbe (jump if above/jump if not below or equal) 
> jumps if both the carry flag (cf) and zero flag (zf) are cleared, indicating `a > b` for unsigned values.

4. jae/jnb (jump is above and equal/jump if not below)
> jumps if the carry flag (cf) is cleared. indicating `a >= b` 

### *signed conditional jumps*

1. jg/jnle (jump if greater/jump if not less or equal) 
> jumps if the zero flag (zf) is cleared and sign flag (sf) equals overflow flag (of) 

2. jge/jnl (jump if greater or equal/jump if not less)
> jumps if sign flag equals overflow flag

3. jl/jnge(jump if less/jump if not greater or equal)
> jumps if sign flag does not equal overflow flag

4. jle/jng (jump if less or equal/jump if not greater)
> jumps if the zero flag is set or the sign flag does not equal overflow flag

### *specific condition jumps*
1. jump based on carry (jc/jnc)
2. jump based on overflow (jo/jno)
3. jump based on sign (js/jns)
4. jump based on parity (jp/jpe (even parity) and jnp/jpo (odd parity) )
( parity flag indicates whether the number of 1 bits in the least significant BYTE is even or odd. )

