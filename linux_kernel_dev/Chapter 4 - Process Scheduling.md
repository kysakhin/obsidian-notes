### some terms
- timeslice: (quantum) is the fixed amount of time that a process is allowed to run on the cpu until the os gives control of the cpu for another process. once the timer is over, then the cpu saves its state, interrupts the process and gives the cpu to another processor. and that process is put into the wait queue. this timeslice is dynamically calculated in the linux process scheduler. 
- the act of a processor voluntarily suspending itself is called yielding. processes are supposed to yield often. the current scheduler for linux is called CFS (completely fair scheduler)

- spinlocks. when a thread wants to access a shared resource, it tries to acquire the lock. but if that resource is already locked, then it "spins" (checks repeatedly) until the lock becomes available. this is unlike other locks that would go to sleep if the resource needed is locked. this can only be useful for critical processes, when the waiting time is rather short.

# for this notes i'll be writing about the previous scheduler of linux, the O(1) scheduler. 

## policy 
a scheduler's policy often determines the overall feel of the system and is responsible for optimally utilizing processor time. 

# i/o-bound versus processor-bound processes 
 processes can be classified into either i/o bound or processor bound. 
 - i/o bound processes can be characterized as a process that spends most of its time submitting and waiting on i/o requests. such a process needs to be spending a lot of time but for shorter durations. this can be: read/write to disk, network communication, keyboard inputs etc. 
 - processor bound processes spend most of their time executing code. these are run less often because there isn't any penalty to the user experience. they do mathematical calculations and use the computational power of the CPU unlike i/o bound processes. since their execution time is dominated by the CPU computation, they tend to be executed less frequently. eg: video rendering, crypto, simulations etc. 

### trade-offs of O(1) scheduler
- it prioritises high priority tasks but can lead to starvation of the lower priority tasks if the high priority dominates the system
- since real time priority tasks are static. if a real time priority is misconfigured then it will monopolize the cpu
- is not very scalable and doesn't handle interactive workloads as efficiently as modern scheduler, the CFS

it can only be used for responsiveness and low overhead systems.


# process priority
the linux kernel implements two separate priority ranges. 
- nice value. how nice is that process to other processes. the range is between -20 to +19. so a higher nice value means that its gonna have less priority and it's gonna let other processes finish up before it. a lower nice value will have the fast-track to the cpu. this nice value also determines how long of a timeslice is needed. a lower nice value receives highest timeslice. but a higher nice value gets a smaller timeslice. 
- real-time priority: ranges from 0-99 generally. but are configurable. real-time processes are at a higher priority since they have a deadline to meet. normal processes, like general computation or something like that have a lesser priority since it can afford being a bit late. 


# the linux scheduling algorithm
the linux scheduler is stored in the *kernel/sched.c* since we are talking about O(1) scheduler, so some of its characteristics are: 
- implement fully O(1) scheduling. every algorithm completes in constant time. 
- implement perfect smp scalability. each processor has its own lock and runqueue
- implement improved smp affinity. this would group certain tasks to one particular cpu. this allows lesser time spent on context-switching. and only migrate tasks from one cpu to another to resolve imbalances. 
- provide good interactive performance. 
- provide fairness. there are no processes that get starved of cpu time, and no processes that dominate the timeslice. 

## runqueues
is a list of runnable processes on a given processor. each processor needs to have one. each runnable process exists on only one runqueue. the runqueue additionally contains some pre-processor scheduling information. here's a general structure
```c
struct runqueue {
	spinlock_t lock;   // spin lock that protects this queue
	unsigned long nr_running;   // number of runnable tasks
	unsigned long nr_switches;  // context switch count
	unsigned long expired_timestamp;   // time of last array swap
 	unsigned long nr_uninterruptible;   // number of uninterruptible processes
	unsigned long long timestamp_last_tick;   //last scheduler tick
	struct task_struct *curr;   // currnetly running task
	struct task_struct *idle;   // this processor's idle task
	struct mm_stuct *prev_mm;   // mm_stuct of the last run task
	struct prio_array *active;   // active priority array
	struct prio_array *expired;   // the expired priority array
	struct prio_array *arrays[2];   // the actual priority arrays
	struct task_struct *migration_thread;   // migration thread
	struct list_head migration_queue;   // migration queue
	atomic_t nr_iowait;   // number of tasks waiting for io
}
```

there are a few macros that are used to obtain the runqueue associated with a given processor. 
*cpu_rq(process)* returns a pointer to the runqueue associated to that process. \
*this_rq()* returns the pointer to the runqueue of the current processor. \

before a runqueue can be manipulated, it must be locked. this prevents modification of the runqueue while the lock-holder is making changes to it. 

## priority arrays
each runqueue contains two priority arrays, the active and expired array. these provide O(1) scheduling. this is old and outdated, since the newer kernel uses O(log n). here's a general structure: 

```C
struct prio_array {
    unsigned long bitmap[BITMAP_SIZE];  // Tracks which priority queues are non-empty
    struct list_head queue[MAX_PRIO];  // Array of linked lists, one for each priority level
    int nr_active; // Number of active (runnable) tasks in this array
};
```

in the O(1) scheduler, both static and dynamic priorities are mapped to a common range. *MAX_PRIO* by default is 140. (0-139). the bitmap is first initialized to 0 (140 times). and now if i wanna run (change their state to runnable) a few tasks of a given PRIORITY, then i can just change the bit in the bitmap\[PRIORITY\] to 1. there is one `struct list_head` for each priority level. 

so the bitmap is a contiguous array of bits that span over the elements of the array. since we need 140 bits, and considering **unsigned long,** 
- on 32-bit systems is of 32 bits size. so you would need bitmap\[5\] to cover the whole range of 140 priorities. 
- on 64-bit systems is of 64 bits size (linux and macos). (32 bit on windows) you would need bitmap\[3\] to cover the whole range of 140 priorities.

bitmap\[0] holds priorities 0-63 \ 
bitmap\[1] holds priorities from 64-127 and so on \ 
and here, indexing starts from right. so if there's something like 0b000.....01, then this means that priority 0 has runnable tasks. \
there's a cpu instruction called fast find first set bit which fetches the first set bit in a bitmap in O(1) time. 

## recalculating timeslices
once a task has exhausted its timeslice, the kernel removes it from the active runqueue. then it recomputes the timeslice for that task before moving it to the expired queue. once there's no more any tasks in the active queue, then the kernel would just need to swap the pointers of the active and expired runqueue. being a lot more efficient. this obviously has some very major trade-offs like starvation if a the active runqueue is continuously being repopulated. 

## calculating priority and timeslice
### priority
a process initiates with a default nice value of 0. then a method called `effective_prio()` returns a tasks's dynamic priority. the method begins with the tasks's nice value and then giving it a bonus or penalty of +5 to -5 based on the interactivity of the task. if a task is more interactive, then it'll get more bonus and gets a higher dynamic priority. on the other hand, if the process is a cpu hog, then it will get a penalty. \
but how exactly does it know if a process is interactive or not? \
if a task spends most of its time sleeping, then it is i/o based. but if it spends most of its time running, then its not an interactive process and hence it would get a penalty. and also interactive processes tend to work in shorter time bursts and are interrupted frequently. 

### timeslice
timeslice is calculated based on that process's static priority. the calculation is the scaling of the static priority into a range of timeslices. 

## sleeping and waking up
processes waiting for a certain resources to become available are put on sleep or blocked state. once a process is put into the sleep state, it removes itself from the runqeueue, (this ensures that the scheduler isn't running it unnecessarily) and gets added into the wait queue. \
and then to wake up, it is the reverse, it marks itself as runnable, removes itself from the wait queue, and joins the runqueue. \
going back, there are two states associated with sleeping, TASK_INTERRUPTIBLE which can be awaken by signals. and another TASK_UNINTERRUPTIBLE which doesn't respond to signals. 

## load balancer
since each processor on a multiprocessor system has its own runqueue, the load balancer ensures that the tasks are equally or almost equally distributed among the cpu cores. it pulls processes from one runqueue to another to relieve imbalances. the steps that it follows are: 
1. find the busiest queue. start with one runqueue, then try to find a runqueue that has atleast 25% more tasks than the current busiest runqueue. if no such runqueue is found, then there is no need for a load balance.
2. then it decides which priority array does it want to pull from that selected runqueue. the expired queue is preferred since its not cache hot (most unlikely to be in the cpu's cache). if the expired queue is empty, then the active queue is the only option. 
3. then it finds the highest priority list that has tasks since it is important to run the higher priority tasks than the lower priority. 
4. then it goes through the tasks of that given priority to find a task that is not running, not prevented to migrate (due to processor affinity) and is not cache hot. and then it pulls that task. 
5. continue this until the runqueues are imbalanced. 

# preemption and context switching 
context switch, the process of switching from one runnable task to another is handled by the kernel. but the kernel must know when to call the next runnable task. for that it uses a flag called as the *need_resched* flag. \
when is the *need_resched* flag set:
- timer interrupts. timer interrupts are a type of interrupts generated by a hardware timer in a computer system at regular intervals. its primary purpose is to allow the kernel to perform periodic tasks like timekeeping, scheduling and managing process execution. so these decrement the current task's time slice. when the time slice is exhausted then it sets the *need_resched* flag. 
- task state changes. when a higher priority task becomes runnable (when it changes its state from sleeping to runnable) then the flag is set
- explicitly. functions to wake up process, yield functions, etc.

### user preemption
when a user-space program is interrupted to allow another process to run. this can happen when:
- time slice of current process is expired
- higher priority process is runnable
- voluntary yield

### kernel preemption
the ability to interrupt a running kernel-space process (such as system call or an interrupt handler) to allow a higher priority process to run. previous versions made kernel tasks non-preemptive. \
unlike user-space processes that are easier and safer to preempt, kernel processes aren't. and there are still a few processes that are non-preemptive. a process in kernel space is safe to be preempted only if 
- no kernel locks or critical sections are active
- no non-preemptible sections of code are being executed
- task is not holding any non-recursive spinlocks
there are also a few safe preemption points like 
- between independent system calls
- during longer computational tasks in kernel space
- waiting for i/o
- after releasing locks and before entering new critical sections.


# Real-Time
linux provides two real-time scheduling policies, *SCHED_FIFO* and *SCHED_RR.* \
SCHED_FIFO implements a simple first in first out policy. when a SCHED_FIFO task becomes runnable, it continues to run until it blocks or explicitly yields the processor. it has no timeslice and it can run indefinitely. only a higher priority SCHED_FIFO or SCHED_RR can preempt a task. if there are two or more tasks of the same priority, then they run round robin, but only yielding when they explicitly chose to do so. \
SCHED_RR on the other hand, use timeslices. each process can run only until it exhausts a predetermined timeslice. here, tasks can also be preempted by a task of a higher priority. \
real time priorities range from 0-99. (lower the higher priority) 
