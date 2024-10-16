this talks about memory leaks in C. before that, you must understand dynamic memory allocation in C, you can find it [here](https://blog-app-wheat-kappa.vercel.app/blog/e47923e4-ac5a-46c5-9582-cbb96b13dbe2)

Once that you have created a memory in heap, it is always a good practice to deallocate it once it has completed its job. since this isn't stored in the stack memory, which by default takes care of deallocating memory once its scope is over, the programmer themselves must take care of deallocating it. \
usually, it isn't much of  a problem, once the program finishes execution, it automatically terminates along with the program. but this is absolutely necessary for programs that don't terminate, like daemons and servers. \
the consequence of not freeing a pointer is that it will start to occupy the free memory and make it harder for future allocation of memory. worst case, the amount of free memory might be exhausted and the application fails or the system slows down. \

but how do i really free a heap memory? \
using the `free()` function \
![](https://blog-pictures.vercel.app/free.png)
to be fair, this isn't enough either. but that is a topic that needs its own special mention. as of writing this blog, i haven't yet written about it.

a memory leak occurs when a programmer creates a memory in heap but forgets to delete / free it. and this allows other heaps to access it. \
so when you call the `free()` function, what really happens is that, 
1. Memory deallocation: it tells the memory management system that the block of memory pointed by that pointer is no longer needed. and that memory can be used for future allocations.
2. Pointer value: however, the pointer still holds the same value. this is called as a dangling pointer. when a pointer points to location that has been deleted. 
3. so what happens to the data? it doesn't get actively erased. it just simply becomes inaccessible. and that memory is no longer allocated to our program. if we did try to access it, and try the dereference it (\*) it leads to unexpected behavior, or it also giving out garbage values.