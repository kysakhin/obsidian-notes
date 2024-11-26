### some linux commands
```bash
ps -el
```

```bash
ps -eo state,uid,pid,ppid,rtprio,time,comm
```

---
this is the default scheduler presently. introduced in kernel version 2.6.23 (2007). cfs aims to divide the cpu time based on task priorities. each task gets its fair share of cpu time. cfs uses a red-black tree which is a self balancing binary search tree to maintain runnable tasks. this was introduced to improve scalability.

and also cfs is only used for normal processes only and real time processes have different policies.
cfs is only used for normal processes so it works only with the nice value priority range (-20 to 19). real time tasks are handled separately with their own dedicated scheduling policies. 

## virtual runtime (vruntime)
this is a mechanism to track fairness. the cfs algorithm picks the process that has the lowest vruntime. lower vruntime translates to the process being more deserving of cpu time. and this is calculated using the formula

```
vruntime = actual_runtime * (1024/process_weight)
```

so here the process weight is calculated using:
```
process_weight = 1024 / (1.25 ^ nice_value)
```

*additionally, cfs also applies granularity to prevent very short time slices and minimum vruntime increements.*

this is actually really smart for a few reasons,
- this calculation of process weight is non-linear so there are more ranges of process weights to choose from. 
- as the nice value gets into the negatives and gets lower, the process weight gets exponentially higher. this translates into exponentially low vruntime, which translates to more number of times that process is chosen to allot cpu time.
- but this also doesn't really monopolize the cpu time either. since vruntime is additive and it adds up the actual runtime every time that process is invoked. and hence vruntime for a higher priority process is added more frequently and helps balance out in some way

for reference, here's how process_weight is affected by nice values
1. **nice (-20):**  weight = 88,817
2. **nice (-10):**  weight = 9,536
3. **nice 0:** weight = 1024 
4. **nice 10:** weight = 109
5. **nice 19:** weight = approx. 15


