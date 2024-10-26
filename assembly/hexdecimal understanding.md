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

