### some terms
- timeslice: (quantum) is the fixed amount of time that a process is allowed to run on the cpu until the os gives control of the cpu for another process. once the timer is over, then the cpu saves its state, interrupts the process and gives the cpu to another processor. and that process is put into the wait queue. this timeslice is dynamically calculated in the linux process scheduler. 
- the act of a processor voluntarily suspending itself is called yielding. processes are supposed to yield often. the current scheduler for linux is called CFS (completely fair scheduler)

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

there are a few macros that are used to obtain the runqueue associated with a given processor. \
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

