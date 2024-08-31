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
### 4. Trace-Shrinking, 
```Sanity Check: Trace-encoder (), Read log and shrink trace ()```


# Results Evaluations
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
eyJoaXN0b3J5IjpbNDQ3NzQyNTk1LDE2MDczMTA3NTQsMjEwMT
Q5MzQzMiwtNTgxNTMyNzQ3LC0xMzc3NzE1ODk0LC0zODc2NDc5
OSwxMjcxNzY0MTYwLC02NjQyMzE1NDAsMTE3NTA3ODM1Niw5MT
AyNDk2MjZdfQ==
-->