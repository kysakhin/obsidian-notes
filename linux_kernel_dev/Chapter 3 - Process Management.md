### definition
a process would include a set of resources such as open files, pending signals, internal kernel data, processor state, a memory address space with one or more memory mappings, one or more threads of execution, and a data section containing global variables. the kernel needs to handle all these efficiently and transparently. \
when a process is created, the operating system allocates a new memory space for it, and the process runs independently of other processes. processes communicate with each other using inter-process communication (IPC) mechanisms, such as pipes, sockets, or shared memory.

characteristics of processes:
- **Heavyweight**: Creating a new process is a heavyweight operation, as it requires allocating new memory space and initializing a new execution context.
- **Isolated**: Each process runs in its own memory space, which provides isolation and protection from other processes.
- **Slow context switching**: Switching between processes is slower due to the overhead of saving and restoring the process context.

### thread
threads of execution, or threads for short are parts of processes that can run independently but shares the same memory space as the rest of the process. \
imagine a process as a notebook. and threads are the different people writing in different sections of the notebook. but writing at the same time. without needing separate notebooks. \
each thread includes a unique program counter, process stack, and set of processor registers. the kernel schedules individual threads and not processes.
threads are scheduled by the operating system to run concurrently, improving system responsiveness and throughput.

characteristics of threads:
- **Lightweight**: Creating a new thread is a lightweight operation, as it only requires allocating a new stack and initializing a new execution context.
- **Shared memory**: Threads share the same memory space, which allows for efficient communication and data sharing.
- **Fast context switching**: Switching between threads is faster due to the reduced overhead of saving and restoring the thread context.


on modern operating systems, the kernel provides two virtualizations: a virtualized processor and a virtual memory. so we are abstracting the fact that there are possibly hundreds or even thousands of other threads running in the background to each thread. the timing of each thread is handled by a scheduler. the scheduler determines which thread runs on which CPU core, when to switch between them, and how to allocate CPU time among all threads in the system. then the kernel keeps track of the state of all threads, (e.g running, waiting, blocked) and ensures they share CPU resources fairly and efficiently. this is pretty much like abstraction in oops.


## process descriptor and the task structure
the kernel stores the list of processes in a circular doubly linked list called the *task list.* each element is a structure of type struct task_struct. which is defined in the *<linux/sched.h>*

### process control block (pcb)
each process has its own block. if you wanna see the full details, linux has a special filesystem in the */proc* directory in root. every single process that is running on the system resides in that directory. to see the full details, for example of a process ID of 1234, then it can be found in */proc/1234/status*. in short, each block has these details: 
- process ID: a unique identifier for each process.
- process state: current state, like running, waiting, sleeping
- program counter: the address of the next instruction to be executed.
- CPUs allowed, memory allowed details
- priority level of processes. 
- interprocess communication (ipc)
- signals. 

### allocating the process descriptor 
the task_struct structure is allocated via the *slab allocator* to provide object reuse and cache coloring. before the 2.6 kernel series, task_struct was stored at the end of the kernel stack of each process. this allowed architectures with few registers, such as x86 to calculate the location of the process descriptor via the stack pointer without using an extra register to store the location. 

---
Here are some key roles the kernel plays:

- **Process Creation**: The kernel creates a new process by allocating memory, initializing the process's virtual address space, and setting up the process's execution context.
- **Thread Creation**: The kernel creates a new thread by allocating a stack, initializing the thread's execution context, and scheduling the thread for execution.
- **Process Scheduling**: The kernel schedules processes and threads for execution, allocating CPU time and resources as needed.
- **Memory Management**: The kernel manages the virtual address space for each process, providing memory allocation and deallocation services as needed.
- **Context Switching**: The kernel performs context switching between processes and threads, saving and restoring the execution context as necessary.
- **Interrupt Handling**: The kernel handles interrupts generated by hardware devices, such as keyboard and mouse events, and dispatches them to the appropriate threads for processing.
- **Resource Management**: The kernel manages system resources, such as file descriptors, sockets, and I/O devices, and provides access to these resources to processes and threads.

---

## how a new process is born
a process starts its life by the `fork()` system call. which creates a new process by duplicating an existing process. so a parent process calls the fork(). the parent resumes execution and the child begins execution at the same place where the call returns. then using the exec() family system call are called in the child process to actually load the things onto the ram.

### Process States
- TASK_RUNNING - this process is either running or in the queue waiting to run. 
- TASK_INTERRUPTIBLE - a sleeping process that is waiting for a signal or some condition to exist. when its satisfied, then the kernel sets the process's state to TASK_RUNNING. 
- TASK_UNINTERRUPTIBLE - these tasks wont be interrupted by signals. not even kill/terminate signal can end it. 
- TASK_ZOMBIE - these processes are processes that have finished their execution but their parent process hasnt read any exit status from that process. 
- TASK_STOPPED - if a process has received a stop/terminate signal. this process is not running nor is eligible to run.