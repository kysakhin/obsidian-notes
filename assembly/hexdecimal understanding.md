yeah so addresses are stored in hexadecimal values. since its pretty easy for both the computer and human to read and understand and compute the binary/decimal equivalent of it.

## signed and unsigned values
so for a hexadecimal value, to determine if that value is positive or negative, we look at the leftmost bit (most significant bit or HO). 
- 7182h is negative
- 2abch is positive
- 0fffh is positive
- ffffh is negative

so positive values are stored normally. but negative values are stored as the 2s complement of it.
so if 
2 is 0002h
-2 will be FFFEh
cuz 1111 1111 1111 1101 is 1s complement, so 1111 1111 1111 1110 is its 2s complement

the only reason we store negatives as their 2s complement is because it simplifies hardware operations. because of 2s complement way, addition and subtraction can be handled in the same circuit. 
ok so when storing, it starts off with the positive equivalent of that number and then performs 2s complement. after performing that, the msb of that number will end up being 1.


## sign and zero extension
since two's complement format integers have a fixed length, a problem develops. if we wanna convert 8bit two's complement to a 16bit value. and vice versa. this can be achieved by something called as sign extension and contraction operations. the 80x86 architecture has fixed byte sizes:
- 8 bit (byte)
- 16 bit (word)
- 32 bit (double word or dword)
- 64 bit (quad word or qword)
The **AX** register holds a 16-bit word, while **EAX** holds a 32-bit double word in 32-bit x86, and **RAX** holds a 64-bit quad word in x86-64.

if you wanna convert a signed lower bit to higher bit, 
if the msb is 1, F pad the hex
if the msb is 0, 0 pad the hex

if you wanna convert unsigned then just 0 pad.

