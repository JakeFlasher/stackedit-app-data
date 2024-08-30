# ChampSim Developing & Debugging
## Useful Github Issues
 > - [# Physical or virtual address](https://github.com/ChampSim/ChampSim/issues/15)
 > -

# Constable feature
## Implemented
### 1.  A tracing tool based on dift-addr, that reads, decodes a tracefile (.xz, .gz) and outputs a decoded comprehensive csv trace, a global-stable target csv trace and a residual csv trace.
``` Usage: ./load_inspect generates a stable_load.bin file and log file tells you how many instructions are left excluding global stable, which is the number of actual simulation instructions for input ```
### 2. ChampSim edited to implement the above logic within the do_cycle() main functions.
``` Note: require the ./load_inspect log file to  ```
- Incurring more overheads during simulation, cause deviation in IPC
### 3. ChampSim edited to read a binary file that contains the #instr count when each global stable load occurs and chooses whether to skip them or not.

- Overheads during simulation is much lower.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTg0NTI1MjgxOSw5MTAyNDk2MjZdfQ==
-->