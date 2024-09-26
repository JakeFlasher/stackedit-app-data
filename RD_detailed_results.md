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
![输入图片说明](https://www.researchgate.net/profile/Mathias-Johanson/publication/221910054/figure/fig4/AS:305074569007107@1449746853302/Histogram-showing-the-frequency-of-failures-in-discrete-intervals-of-mileage.png)
> footprint: unique number of memory addresses accessed during a sliding window.

1. Global stable load instructions (GSL)
2. Partitioned Value Leaking Address Detection
3. Profile every re-access of memory address if footprint is greater than threshold  
4. Partitioned Random Sampling on the profiled instructions after **Minimum** footprint (20%) is reached  

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

> 1. 1M-interval, 500 intervals: ~380 matches, averge <1000 (remaining)
	> 1.1 Total counts of tiny RD (< 128K) is more
> 2. 5M-interval, 100 intervals: ~600 matches, average ~20000 (remaining)
	> 2.1 Total counts of tiny RD (< 128K) is fewer

| Methods               | IPC_Var     | Rowbuffer_Hitrate | Sim_Speedup | Instr_Reduced | IPC curve dtw |
|-----------------------|-------------|-------------------|-------------|---------------|---------------|
| global stable load    | 5.879529269 | 0.181407155       | 105.6660857 | 7.664156737   | 0.209375499   |
| RD_32                 | 6.710888837 | 2.763657624       | 104.8526892 | 7.664156737   | 0.2308437     |
| fp_filter_small       | 11.90931969 | 2.793926126       | 86.66118944 | 7.6636689     | 0.881613808   |
| fp_filter_large       | 4.162173119 | 5.110396513       | 106.3684418 | 7.6636689     | 0.235623326   |
| value leaking         | 41.20151088 | 14.81638258       | 165.7313935 | 7.523979186   | 0.936814424   |
| *Heuristic Stable Load | 4.090672311 | 1.279531597       | 108.5294026 | 11.23777197   | 2.774276379*   |


| Methods               | Cache_Miss  | Cache_Latency | TLB_Miss    | TLB_Latency |
|-----------------------|-------------|---------------|-------------|-------------|
| global stable load    | 0.056891792 | 0.407167477   | 0.6373364   | 0.867442016 |
| RD_32                 | 3.150721162 | 0.651987732   | 0.364460336 | 0.805930442 |
| fp_filter_small       | 0.59140465  | 1.998686053   | 3.20412126  | 2.08613268  |
| fp_filter_large       | 7.461245407 | 2.415868282   | 6.995133191 | 1.493968173 |
| value leaking         | 3.394935434 | 6.000382796   | 22.16003187 | 4.371017967 |
| *Heuristic Stable Load | 3.334038114 | 273.7269483   | 4.099679747 | 258.6522957 |

# Use reuse-distance to select LOAD/STORE instructions

| Literal RD threshold (64x64x12) | Cache Miss Error (geomean) | Cache Latency Error (geomean) | IPC Error (geomean) | Avg Speedup | Avg Instr Reduction  |
|-------------------------|----------------------------|-------------------------------|---------------------|-------------|----------------------|
| 49152                   | 20.52766049                | 4.288978837                   | 8.119020266         | 112.643219  | 15.23203454          |
| 24576                   | 11.46614849                | 2.779277441                   | 9.258425954         | 110.4203876 | 14.11482383          |
| 12288                   | 5.401353645                | 1.937779051                   | 8.847979845         | 110.0942809 | 13.07478774          |
| 512                     | 2.509287692                | 0.857150494                   | 7.149924391         | 108.4858667 | 10.72975028          |
| 256                     | 2.829008832                | 0.794718217                   | 7.051488982         | 108.6694079 | 10.56544998          |
| 64                      | 2.941739265                | 0.677701461                   | 6.876926394         | 107.6435901 | 10.29106503          |

| RD Histogram Cut-off | Cache Miss Error (geomean) | Cache Latency Error (geomean) | IPC Error (geomean) | Avg Speedup | Avg Instr Reduction  |
|----------------------|-------------|---------------|-------------|-------------|----------------|
| 10th percentile      | 27.33536502 | 6.478540375   | 18.93003391 | 146.3384793 | 18.38060444    |
| 20th percentile      | 28.86031297 | 6.772132787   | 18.53796118 | 146.4992429 | 18.66504081    |
| 30th percentile      | 21.30779885 | 11.82680148   | 14.26130813 | 178.0835691 | 19.65638909    |
| 40th percentile      | 22.643441   | 12.30648968   | 25.36967507 | 193.2707652 | 20.50298836    |

| Sorted RD Density Cut-off          | Cache Miss Error (geomean) | Cache Latency Error (geomean) | IPC Error (geomean) | Avg Speedup | Avg Instr Reduction |
|------------------------------------|----------------------------|-------------------------------|---------------------|-------------|---------------------|
| 10th percentile total Instructions | 2.158186267                | 0.711853104                   | 3.668325697         | 105.1641335 | 5.780579489         |
| 20th percentile total Instructions | 3.31476966                 | 0.964130122                   | 6.826707658         | 106.1212914 | 9.390411534         |
| 30th percentile total Instructions | 5.809361025                | 1.397870322                   | 5.649304913         | 109.5223588 | 11.41986073         |
| 40th percentile total Instructions | 7.001040846                | 1.367277811                   | 10.33490331         | 117.0958631 | 13.68665289         |

 ![输入图片说明](https://raw.githubusercontent.com/JakeFlasher/stackedit-app-data/refs/heads/master/img/combined.png)


1. 由于所有的Dynamic Time Warping都基本位于0.18-0.35之间，与ground truth差距较小，差值主要跟instruction reduction有关，可以基本认为按照小RD删除指令对IPC Curve影响很小 
2. 对IPC和Cache影响小的指令probably是被删除后加速比提升也很小的指令 ：**加速比小，在仿真中latency和cycles数少，往往是data reuse (RD小)**
3. 删除指令对IPC和Cache的影响probably是相互独立的：**删除某些指令，对Cache影响很小但是对IPC影响巨大**
4. 在利用RD绝对值进行筛除指令时，IPC Error和Instruction Reduction在所有情况下都有相当高的correlation (0.97~0.99)，哪怕在RD=48K，此时cache error已经很高的情形。

TODO:
4. rd范围和cache关系
5. 全局上删除，时间轴收缩可能不等比例， 平均ipc可能影响较大，局部删除，可能保存了两者等比例变化
6.   partitioned rd具有全局和局部的性质
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU0NjI4MjUxOCwxNTkxNDc1OTc5LDk2Mj
EwMjIwOSwxNTc2NjE3MTg5LC0xMTc1OTQ2NzI4LDE2NTY0MjA4
NjgsLTYyNTc3NzU1MiwxMTczOTc1NDYxLC0xNzkwODU2NjM4LC
0xNDczOTAyNjkyLC0xNDU4NTk2ODMxLC0xNTI1NTc0NDc0LDEy
NDM2NTAyNzYsMTg2MzI1OTc5MywtNDg3MTgzNTM5LC0xMzYyMz
E4MDMsLTg3MjE2NzMsLTE5MTA5MjIxODMsMjA5NjgwMDgyM119

-->