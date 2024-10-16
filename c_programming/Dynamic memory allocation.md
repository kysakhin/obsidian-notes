this blog talks about dynamic memory allocation in heap memory. if you are not familiar with it, then you can check out [C Memory Management](https://blog-app-wheat-kappa.vercel.app/blog/5a24351f-8f08-4afc-86ed-02669571f192)


Some things about the heap memory is that, heap memory is visible to all threads. this can be unsafe. this means that multiple threads can access and modify heap memory. this can lead to something called a race condition (where two threads read or write to the same memory location at the same time). to overcome this, locks or other synchronization methods are used to access particular section at a time.

so the heap memory is managed by malloc, realloc, free, and calloc, which are functions in the `stdlib` library. so when you initiate an array, for example like this.

### simple array program
![](https://blog-pictures.vercel.app/malloc.png)

this basically makes an array of size n. 
the function *malloc* returns a pointer. it creates an object in the heap memory and returns a pointer to that object. that's why we are storing pointer value in the stack memory here, inside the */*ptr*

however this might be overwhelming, \
in modern version you can get away without mentioning the **(int \*)** as the default cast type is void. mentioning it is just a better practice. 


### how to modify array?
arrays and pointers in C are closely related to each other. it is as simple as, 
![](https://blog-pictures.vercel.app/arrayManip1.png)

this is equivalent to, \
![](https://blog-pictures.vercel.app/arrayManip2.png)

pointer arithmetic takes care of index/position of the array. 