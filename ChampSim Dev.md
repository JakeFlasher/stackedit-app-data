# ChampSim Developing & Debugging
## Useful Github Issues
 > - [# Physical or virtual address](https://github.com/ChampSim/ChampSim/issues/15)
 > -

# Constable feature
## Implemented
### 1.  *Trace-Inspector* based on dift-addr, that reads, decodes a tracefile (.xz, .gz) and outputs a decoded comprehensive csv trace, a global-stable target csv trace and a residual csv trace.
``` Usage: ./load_inspect generates a stable_load.bin file and log file tells you how many instructions are left excluding global stable, which is the number of actual simulation instructions for input. ```
### 2. *ChampSim-GST* edited to implement the above logic within the do_cycle() main functions.
``` Note: require the ./load_inspect's log file to retrieve the actual simulation instructions. ```
```Sanity Check: passed, see log test v.s. base (perfs, mem footprints), but IPC is affected (cycles calculation affected) ```
- Incurring more overheads during simulation, cause deviation in IPC
### 3. *ChampSim-Skip* edited to read a binary file that contains the #instr count when each global stable load occurs and chooses whether to skip them or not.
``` Note: require the ./load_inspect's log file and stable_load.bin to retrieve the actual simulation instructions and skip them in specific instruction count. ```
- Overheads during simulation is much lower.
### 4. No changes to ChampSim. Instead, create a custom trace profiler to filter and synthesize the traces as pre-processing.
- Low overheads during simulation, IPC calculation integrity secured.
### 4. Trace-Inspector
```Sanity Check: Trace-encoder (), Read log and shrink trace ()```

## Novelty
### 1. Where's your novelty besides the application of Constable?
### 2. Need to further increase speedups, maybe put a more radical strategy in reducing instructions?
### 3. Need to corporate machine learning.
### 4. We have GAP(470M) SPEC06(970M) and SPEC07(1990M), three lengths for each benchmarks. However, can we reduce the profiling pre-processing stage? maybe just 1 overall profile for 1 trace, instead of sliding-window profiling 1 trace multiple times.
## Experiments
### Traces (GAP, SPEC06, SPEC17, Google Workloads)
#### 1. Baseline (vanilla ChampSim + vanilla configuration)
#### 2. [Berti: an Accurate Local-Delta Data Prefetcher (MICRO-22)](https://dl.acm.org/doi/10.1109/MICRO56248.2022.00072)
- [Github Source](https://github.com/agusnt/ChampSim/tree/master/prefetcher/berti)


## Algorithms Set-up
### 1. GSL
#### 1.1 GSL + DIFT_leak preserve (10%) => (
#### 1.2 GSL + DIFT_leak throwing (0.2%) => (intersection(a,b) = a)
### 2. GSL+RD (hard threshold, fast profiling)
### 3. GSL + RD (soft threshold, very very slow) + DIFT_leak preserving
### 
# Results Evaluations
## Correlation Heatmaps 
> 1. A correlation heatmap is created for each of the benchmark (spec06, spec17, GAP) after summarzing the difference from results across all trace files.
> 2. *IPC_variation* and *Simulation_Speedup* is found to be strongly correlated (~0.9) for all 3 benchmarks
	> 2.1 Maybe train a learning model to correct the IPC value according to speedups?
	> 2.2. According to 2.1, in order for the learning model to work well, we need to keep track of the vanilla_trace simulation time everytime we encounter a new trace, which is totally nonsense. Otherwise, there's no speedup if we have to run the vanilla_trace over again.
	2.3 Elaborate the correlation further into small, more fundamentals building blocks that can be monitored within the shortened trace simulation.
> 3. [Does correlation between variables and class label affect building a good classifier?](https://stats.stackexchange.com/questions/263531/does-correlation-between-variables-and-class-label-affect-building-a-good-classi)
> 4. [Use of correlation analysis in machine learning?](https://stats.stackexchange.com/questions/481457/use-of-correlation-analysis-in-machine-learning)
## Normalization of differences 
>  1.  [Bottom to top explanation of the Mahalanobis distance?](https://stats.stackexchange.com/questions/62092/bottom-to-top-explanation-of-the-mahalanobis-distance)
>  2.  [z-score vs min-max normalization](https://stats.stackexchange.com/questions/547446/z-score-vs-min-max-normalization)
>  3. [Demystifying Data Normalization in Machine Learning](https://medium.com/@weidagang/demystifying-machine-learning-normalization-0cdb8b281234#:~:text=Min-max%20normalization%20scales%20the,a%20standard%20deviation%20of%201.)


## Comparison of zero leading values
### The problem you're describing revolves around calculating a weighted average of differences, where the weights are determined by the proportion of the baseline values. This approach ensures that changes in small components do not disproportionately affect the final score.
```CPU 0 cumulative IPC: 0.5272 instructions: 100000004 cycles: 189696853
CPU 0 Branch Prediction Accuracy: 96.02% MPKI: 5.379 Average ROB Occupancy at Mispredict: 105.9
Branch type MPKI
BRANCH_DIRECT_JUMP: 1e-05
BRANCH_INDIRECT: 0
BRANCH_CONDITIONAL: 5.379
BRANCH_DIRECT_CALL: 0
BRANCH_INDIRECT_CALL: 0
BRANCH_RETURN: 0
```
```
CPU 0 cumulative IPC: 0.5532 instructions: 65071979 cycles: 117619171
CPU 0 Branch Prediction Accuracy: 96.18% MPKI: 7.473 Average ROB Occupancy at Mispredict: 96.6
Branch type MPKI
BRANCH_DIRECT_JUMP: 0.000461
BRANCH_INDIRECT: 0
BRANCH_CONDITIONAL: 7.473
BRANCH_DIRECT_CALL: 0
BRANCH_INDIRECT_CALL: 0
BRANCH_RETURN: 1.537e-05
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTc3NDYxNjkwMiw2NDEyMzIwNjMsLTc1Nz
YwMjU4NCwxNTIwMTA2OTA1LDY3NTgyMzk5MSwtNDU5MzQ0OTEx
LC0xMTY0MjYyNTA1LC0xODEyNjEyODkxLDQ0Nzc0MjU5NSwxNj
A3MzEwNzU0LDIxMDE0OTM0MzIsLTU4MTUzMjc0NywtMTM3Nzcx
NTg5NCwtMzg3NjQ3OTksMTI3MTc2NDE2MCwtNjY0MjMxNTQwLD
ExNzUwNzgzNTYsOTEwMjQ5NjI2XX0=
-->