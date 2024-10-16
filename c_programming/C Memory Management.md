Memory management in C works in intriguing ways really. For that first we need to understand Memory Layout of C programs

# Memory Layout of C programs
![](https://media.geeksforgeeks.org/wp-content/uploads/memoryLayoutC.jpg)

Starting from the bottom, \
1. `text`: this is where your code is stored. When executed, this section is in read-only mode. \
As a memory location, a text segment is placed below the stack or heap section to prevent them from overwriting its content. 
2. `initialized data:` sometimes called data segment, this is where global variables are stored. the variables that are declared and assigned a value are stored here. sometimes they are also classified into read-write and read-only segments
3. `uninitialized data(bss):` bss stands for block starting symbol. here the global variables that are only declared are stored. these variables aren't assigned a value as of yet. so they are are initialized to 0.
4. `heap:` as shown in the diagram, heap memory isn't fixed, indicated by the dotted lines. so this means that heap can grow upwards. but what exactly is heap?\
	this is not to be mistaken with heap data structures.\
to understand heap, we need to understand stack first.
5. `stack:` stack contains all the static data. static data are the data who's memory space stays fixed and is not allowed to change. any normal data type like making \
```
int i=5;
int arr[10] = {1,2,3,4,5,6,7,8,9,10}
```
and more like this, are all static data type. once memory is allocated to these, it cannot be changed. this is stack data type. \
stack, like the word says btw, variable references are stored in a stack data structure. LIFO, so once a function ends, then the variables declared in that scope are deallocated memory automatically. \

now getting back to heap, 
heap is dynamically allocated memory. as and when needed, heap can grow towards the stack memory. and likewise. unlike stack, the memory in heap can be managed manually, meaning you can resize it, shrink it and grow it. because of this very reason, the programmer must also take care of deallocating the variables too hehe. 

looking back at the diagram, the stack section is adjoined with the heap section. and when the stack pointer meets the heap pointer, free memory will be exhausted.

---
## fun facts
accessing stack memory is generally faster than accessing heap memory. this boils down to the fact of how it is managed. \
1. automatic management: stack data structure follows LIFO. when a function is called, the local variables, function parameters, and return address are automatically pushed onto the stack. When the function returns, these are popped off the stack. \
2. cache friendly: stack memory is closely packed in the memory and hence you are not left with memory fragmentation. i.e, having gaps in your memory. each time a function is called, a stack frame (containing local variables, return addresses, etc.) is created and placed on the stack.

on the other hand, heap may be more flexible but it comes at a cost.
1. dynamic management: allocating memory in heap involves searching for a large enough block of free memory which takes time. you also have to manage it manually, using the `free()` function. this adds overhead and can lead to problems like memory fragmentation which can make future allocations slower. 
2. no cache optimization: heap memory are typically larger and spread out and so the cpu can't load or predict access patterns as efficiently as it can with stack. 

---

