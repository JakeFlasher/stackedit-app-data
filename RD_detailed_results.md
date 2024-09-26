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


| name                  | IPC_Var     | Rowbuffer_Hitrate | Sim_Speedup | Instr_Reduced | ipc_distances_dtw |   | name                  | IPC_Var     | Rowbuffer_Hitrate | Sim_Speedup | Instr_Reduced | ipc_distances_dtw |
|-----------------------|-------------|-------------------|-------------|---------------|-------------------|---|-----------------------|-------------|-------------------|-------------|---------------|-------------------|
| bc-0.trace.gz.syn_    | 85.35       | -13.93            | 172.25      | -7.57         | 1.831260984       |   | bc-0.trace.gz.syn_    | -5.49       | 3.64              | 103.98      | -7.57         | 0.101451008       |
| bc-12.trace.gz.syn_   | 149.2       | -29.37            | 206.02      | -8.11         | 1.620202784       |   | bc-12.trace.gz.syn_   | -5.23       | 3.48              | 104.71      | -8.19         | 0.139239929       |
| bc-3.trace.gz.syn_    | 43.1        | -1.44             | 140.14      | -6.41         | 1.520256688       |   | bc-3.trace.gz.syn_    | -5.43       | 4.51              | 102.21      | -6.42         | 0.105278257       |
| bc-5.trace.gz.syn_    | 31.47       | -10.77            | 130.34      | -6.07         | 1.584932784       |   | bc-5.trace.gz.syn_    | -5.27       | 3.51              | 103.85      | -6.47         | 0.107313202       |
| bfs-10.trace.gz.syn_  | 43.51       | 11.33             | 148.02      | -7.44         | 1.07686838        |   | bfs-10.trace.gz.syn_  | -9.48       | 7.59              | 101.06      | -7.45         | 0.964603578       |
| bfs-14.trace.gz.syn_  | 37.12       | 4.59              | 142.99      | -7.31         | 0.985100377       |   | bfs-14.trace.gz.syn_  | -8.63       | 4.87              | 101.13      | -7.32         | 0.752651482       |
| bfs-3.trace.gz.syn_   | 42.47       | 14.42             | 147.72      | -7.4          | 1.061042607       |   | bfs-3.trace.gz.syn_   | -8.94       | 8.36              | 101.32      | -7.41         | 0.915877636       |
| bfs-8.trace.gz.syn_   | 37.38       | 4.07              | 144.98      | -7.85         | 1.157300645       |   | bfs-8.trace.gz.syn_   | -9.09       | 4.42              | 101.85      | -7.85         | 0.680714072       |
| cc-13.trace.gz.syn_   | 66.33       | 37.71             | 177.18      | -10.55        | 0.543451842       |   | cc-13.trace.gz.syn_   | -6.28       | 3.67              | 106.62      | -10.55        | 0.104148136       |
| cc-14.trace.gz.syn_   | 59.43       | 33.25             | 169.78      | -10.03        | 0.603311277       |   | cc-14.trace.gz.syn_   | -6.18       | 3.48              | 106.53      | -10.03        | 0.135757956       |
| cc-5.trace.gz.syn_    | 56.74       | 31.6              | 165.36      | -9.72         | 0.532678632       |   | cc-5.trace.gz.syn_    | -6.23       | 3.68              | 105.72      | -9.72         | 0.1061152         |
| cc-6.trace.gz.syn_    | 64.83       | 35.67             | 177.1       | -10.64        | 0.648744592       |   | cc-6.trace.gz.syn_    | -6.5        | 2.88              | 106.55      | -10.64        | 0.089217779       |
| pr-10.trace.gz.syn_   | -5.56       | 14.45             | 96.8        | -1.94         | 0.191647909       |   | pr-10.trace.gz.syn_   | -1.17       | -1.11             | 100.55      | -1.94         | 0.177230767       |
| pr-14.trace.gz.syn_   | 2.67        | 1.08              | 106.6       | -1.94         | 0.1274068         |   | pr-14.trace.gz.syn_   | 0.06        | -2.23             | 103.1       | -1.94         | 0.219808872       |
| pr-3.trace.gz.syn_    | 4.96        | 0.05              | 108.53      | -1.94         | 0.207946248       |   | pr-3.trace.gz.syn_    | 0.17        | -2.25             | 103.08      | -1.94         | 0.208601391       |
| pr-5.trace.gz.syn_    | 3.98        | 0.38              | 106.93      | -1.94         | 0.14502978        |   | pr-5.trace.gz.syn_    | 0.32        | -2.33             | 102.48      | -1.94         | 0.114611324       |
| sssp-10.trace.gz.syn_ | 180.21      | 424.43            | 298.91      | -23.18        | 4.283413128       |   | sssp-10.trace.gz.syn_ | -16.56      | 23.4              | 119.15      | -24.92        | 0.370942184       |
| sssp-14.trace.gz.syn_ | 180.18      | 422.49            | 298.69      | -23.19        | 3.605876221       |   | sssp-14.trace.gz.syn_ | -16.46      | 23.13             | 119.08      | -24.92        | 0.545722698       |
| sssp-3.trace.gz.syn_  | 180.86      | 426.08            | 296.26      | -23.19        | 5.369568746       |   | sssp-3.trace.gz.syn_  | -16.43      | 24.02             | 119.38      | -24.92        | 0.405566106       |
| sssp-5.trace.gz.syn_  | 179.82      | 419.19            | 298.09      | -23.18        | 4.206444567       |   | sssp-5.trace.gz.syn_  | -16.58      | 23.6              | 118.84      | -24.92        | 0.376265092       |
| value leaking         | 41.20151088 | 14.81638258       | 165.7313935 | 7.523979186   | 0.936814424       |   | fp_filter_large       | 4.162173119 | 5.110396513       | 106.3684418 | 7.6636689     | 0.235623326       |




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
4. rd范围和cache param关系（）
5. 2. 全局上删除，时间轴收缩可能不等比例， 平均ipc可能影响较大，局部删除，可能保存了两者等比例变化
6.   partitioned rd具有全局和局部的性质
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTc3MDc3OTY4NCw5NjIxMDIyMDksMTU3Nj
YxNzE4OSwtMTE3NTk0NjcyOCwxNjU2NDIwODY4LC02MjU3Nzc1
NTIsMTE3Mzk3NTQ2MSwtMTc5MDg1NjYzOCwtMTQ3MzkwMjY5Mi
wtMTQ1ODU5NjgzMSwtMTUyNTU3NDQ3NCwxMjQzNjUwMjc2LDE4
NjMyNTk3OTMsLTQ4NzE4MzUzOSwtMTM2MjMxODAzLC04NzIxNj
czLC0xOTEwOTIyMTgzLDIwOTY4MDA4MjNdfQ==
-->