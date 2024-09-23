- 
## Definition of *Global Stable Loads*
- ***A dynamic load instance I2 of a static load instruction I is bound to fetch the same value from the same memory location as the previous dynamic instance I1 of the same static load instruction when the following two conditions are satisfied.*** 
	> 	-  Condition 1: None of the source registers of I has been written between the occurrences of I1 and I2. 
	> 	-  Condition 2: No store or store request has arrived to the memory address of I1 between the occurrences of I1 and I2. 

> Satisfying Condition 1 ensures that I2 would have the same load address as I1, and thus the address computation operation of I2 can be safely eliminated. Satisfying Condition 2 ensures that I2 would fetch the same value from the memory as I1, and thus the data fetch operation of I2 can be safely eliminated.

### Correction of implementation
# Tracking Last Occurrences lncorrectly
```cpp
if(last occurrence.find(address) == last occurrence.end()) {
	last occurrence[address] = i; 
} else {
	// Conditions are evaluated here
	// ... 
	last occurrence[address]= i;
}
```
The current implementation tracks the last occurrence of a **memory address** rather than the last occurrence of a **static load instruction**. In Constable's definition,.conditions are applied to **dynamic instances of the same static load instruction** (i.e.. sameprogram counter or instruction pointer ip).

Using the corrected algorithm for finding **global stable load instructions (GSL)** during simulation execution, we found that **GSL has very little impact on both IPC (5%) and cache miss (3.2%) measurement** and will **boost simulation speed by ~125%** if removed without a big loss on overall performance metrics.
# From Coincidences
Though in simulation, global stable loads may have nothing to do with specific peformance metrics. Since constable presented a micro-architecture-based approach in modern pipelines in order to ensure the correct program execution while reducing redundant load instructions marked as global stable load as to improve performance. However, we do observe a seemingly unique set of instructions of which if removed, they did very impact to cumulative IPC and cache miss change thus can also be eliminated to boost simulation. Therefore, the following experiments setup aim to :
1. Determine whether it is purely the condition of global stable load that contributes to the unique phenomenom or removing other LOAD/STORE instructions also have similar effects.
1. If global stable load is not the superset of such a conditonal set, find such a condition that it captures all the LOAD/STORE instrutions that have this effect. 
# Clarifications
>All the **traces** used are from CRC2, DPC3, where -o3 optimization is used when compiling these benchmarks.
Also, **Constable** paper also showed the existence of global stable loads in off-the-shelf X86 binaries after -o3 optimization.
# Contrast Analysis
We did a set of experiments **varying the condition used to select load/store instructions to remove from the whole trace**, while ensuring the same **total population** and **discrete distribution** (measured in 500 equal intervals across the whole trace) of such load/store candidates.
![输入图片说明](https://media.geeksforgeeks.org/wp-content/uploads/20231004173705/Histogram.webp)
1. Global stable load instructions (GSL)
2. Partitioned Rese-distance Pruning
3. Partitioned Value Leaking Address Detection
4. Partitioned Random Sampling on Minimum footprint-saturated instructions

We measure interested performance metrics in 3 major categories:
- General: IPC, Simulation Speedup, Instructions Reduced
- Cache: cache miss, cache latency (L1D, L2C, LLC, DTLB, STLB)
- Memory: rowbuffer hitrate, total memory cycles 
### Extending the concpets 
# Global  Stable Store Instructions

> Definition: A store instruction is globally stable if it writes the same value to the same memory location across dynamic instances when its inputs have not changed.
Conditions for Store Stability:
> - Condition 1: The source registers and memory operands providing the store address and data have not been modified since the last occurrence.
>- Condition 2: No other instruction has modified the target memory location since the last store.
>-  Tracking Inputs and Outputs: Monitor the source registers for both the address and data, as well as the memory location being written.
Memory State Consistency: Ensure that no intervening stores have modified the memory location.
>- Simulation Optimization: If conditions are satisfied, the store can be considered redundant and potentially eliminated in simulation, reducing memory operation overhead.

##### However, it turned that *global stable stores* defined like the above only consist  of ~0.6% total instructions from the traces, compared to *global stable loads* that typically consist of  ~10-20%, *global stable stores* are negligible.



Effective global stable load instructions can be characterized as a on simulation

> 1. 1M-interval, 500 intervals: ~380 matches, averge ~1000 (remaining)
	> 1.1 Total counts of tiny RD (< 128K) is more
> 2. 5M-interval, 100 intervals: ~600 matches, average ~20000 (remaining)
	> 2.1 Total counts of tiny RD (< 128K) is fewer
<!--stackedit_data:
eyJoaXN0b3J5IjpbOTQzODg4MDAsMTEzMzE2NDcxNSwtNDA3Mz
cwMTUxLC0yMzExMzg0NTQsLTMxMjQyODMwNywtMjA2ODM3Njkx
MywtMTg0NTE5NTMxMywtNzQ1MDA3ODAzLC0xNTAzMDY2NTk4XX
0=
-->