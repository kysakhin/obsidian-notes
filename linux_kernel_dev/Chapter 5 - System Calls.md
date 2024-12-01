# scheduler related system calls
relating back to the previous chapter, linux provides a family of system calls for the management of scheduler parameters. like

|       system call        |             description             |
| :----------------------: | :---------------------------------: |
|          nice()          |     sets a process's nice value     |
|   sched_setscheduler()   | sets a process's scheduling policy  |
|   sched_getscheduler()   | gets a process's scheduling policy  |
|     sched_setparam()     | sets a process's real time priority |
|     sched_getparam()     | gets a process's real time priority |
| sched_get_priority_max() | gets the highest real time priority |
| sched_get_priority_min() | gets the lowest real time priority  |
| sched_rr_get_interval()  |  gets a process's timeslice value   |
|   sched_setaffinity()    | sets a process's processor affinity |
|   sched_getaffinity()    | gets a process's processor affinity |
|      sched_yield()       |  temporarily yields the processor   |

# introduction
system calls are a set of interfaces by which processes running in user-space can interact with the system. \
![](https://i.sstatic.net/DzNLZ.png) 

system calls provide a layer between hardware and user-space processes. this layer has 3 main purposes:
- acting as an abstracted hardware interface for user applications. when reading or writing a file, the application does not need to worry about the type of disk, media or filesystem type.