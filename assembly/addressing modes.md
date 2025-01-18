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
signed and unsigned integers use different flags for comparisions. 

## unsigned comparision
since they dont have negative values, comparision is simply done by subtraction. and from the result, the **Carry Fag (CF)** is set. \
if CF = 1, that means that a borrow occurred, meaning that the first number was lesser than the second. \
if CF = 0, that means that there was no borrow involved, and hence the second number was the lesser one. \
along with this, the Zero Flag (ZF) is also used to track equality

## signed comparision
since signed integers use their MSB for sign, we need to consider the **Sign Flag (SF)** and the **Overflow Flag (OF)** for results. \
if SF = 1 means that the result of the subtraction was negative. \
if OF = 1 means that a large negative number was subtracted from a positive number. \ 

example:
- AL = 0x7F (127 in decimal) and BL = 0x80 (-128 in decimal).
- after `cmp AL, BL`:
    - SF = 0 because the result (127 - (-128)) is positive.
    - OF = 1 because the result exceeds the range of a signed 8-bit integer.

---
