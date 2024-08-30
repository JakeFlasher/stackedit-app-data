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
```Sanity Check: passed, see log test v.s. base```
- Incurring more overheads during simulation, cause deviation in IPC
### 3. *ChampSim-Skip* edited to read a binary file that contains the #instr count when each global stable load occurs and chooses whether to skip them or not.
``` Note: require the ./load_inspect's log file and stable_load.bin to retrieve the actual simulation instructions and skip them in specific instruction count. ```
- Overheads during simulation is much lower.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTY2NDIzMTU0MCwxMTc1MDc4MzU2LDkxMD
I0OTYyNl19
-->