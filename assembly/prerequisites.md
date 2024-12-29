## endianness
refers to the order in which bytes are stored or transmitted in a memory. 
1. big-endian
2. little-endian

## big-endian
the msb of the data is stored at the lowest memory address.

for example, if you are storing 0x12345678

| memory address | data |
| -------------- | ---- |
| 0x00           | 0x12 |
| 0x01           | 0x34 |
| 0x02           | 0x56 |
| 0x03           | 0x78 |


## little endian
the lsb of the data is stored at the lowest memory address.

for a number 0x12345678

| memory address | data |
| -------------- | ---- |
| 0x00           | 0x78 |
| 0x01           | 0x56 |
| 0x02           | 0x34 |
| 0x03           | 0x12 |
|                |      |

## so why one over the other

### little-endian
- more natural for systems that perform arithmetic from lsb to msb. which is common in modern processors like Intel and AMD
- allows for easier incrementation
### big-endian
- more human readable. more common on older systems like PowerPC, SPARC
- often used in network protocols.


so naturally assembly for intel processors is little-endian. 


## floating point decimals
### single precision real numbers
they use 32 bits to store decimal values. and its divided something like this
1. 1 bit for sign
2. 8 bits for exponent
3. 23 bits for mantissa/significand

using these, floating numbers are calculated like
(-1)^sign x 2^(exponent-bias)  x 1.Mantissa

1. **sign bit** pretty self explanatory. 0 for positive 1 for negative

2. **exponent**
stores the scaled component of the number. its also very cleverly managed. exponent can be resolved to positive or negative values. 
there's something called as bias. for single precision floating points, the bias is 127. the actual exponent is calculated by

actual exponent = stored exponent - 127
if we have something like 10000010 in binary, this is 130 in decimal. 
so actual exponent = 130 - 127 = 3
this 3 becomes the actual exponent. something like
2^(actual exponent) = 2^3 = 8

3. mantissa
it stores the significant digits of the decimal part. like how in binary decimals, the place values are something like 1/2 1/4 1/8 1/16 and so on....
mantissa is calculated basically something like 1 + (fractional part)
its 1.fractional part. fractional part can be 0. fractional part starts from 2^-1 and goes on until the end.

