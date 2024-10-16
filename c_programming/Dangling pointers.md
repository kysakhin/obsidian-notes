okay so now like a good programmer you have deallocated the memory that you have used for a heap data structure. if you are unfamiliar with these terms, check out [Freeing and Memory Leaks](https://blog-app-wheat-kappa.vercel.app/blog/f80f3b8d-718d-40ef-9a96-48908b9d24ad)

the story doesn't end at deallocation. once you have done that, the value of the pointer that was once pointing to it doesn't change. so it is still the same. this is called a **dangling pointer.** 

this is bad for several reasons:
1. undefined behavior: when you dereference a dangling pointer, it might crash or throw a garbage value. 
2. memory corruption: if a dangling pointer is used to write data to a memory that has been freed, it can overwrite data that belongs to other parts of program. this can lead to unexpected behavior or corruption of data. 

here's an example
this is a simple code that demonstrates what happens if we try to access a dangling pointer. \
![](https://blog-pictures-akhins-projects.vercel.app/dangptr1.png) \
\
here we make a small array of 3 elements using *malloc()* and try to also print the pointer value. \
here's the output \
![](https://blog-pictures-akhins-projects.vercel.app/dangptr2.png)\

so here, after deallocating, we can see that accessing the first value of the array would display a garbage value. but the pointer still continues to point to that same location. \
but the line **ptr = NULL** would change the pointer value. and hence when we try to access it then it tells segmentation fault, like here. \
if one wants to, they can also change the value after the memory has been freed aswell. heh. sometimes ignorance is bliss.

![](https://preview.redd.it/how-memory-management-evolved-v0-btbxtye236za1.png?auto=webp&s=56d0b5c2ef25f27c268692b89fdbef4c9f1a7c6c)