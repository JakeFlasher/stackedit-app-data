## For the first time, introduce the concept of "time-domain" and "freq-domain" in workload synthesis
The search space is almost infinite, you cannot grasp every aspect at once. Once got on some aspects, the others will inevitably get ignored.

If you cannot forsee some aspect from one specific domain, then either means these two aspects are orthogonal or it is not included.

You need to focus on some specific domain to restrict the search space as well as control variables.


### Time-domain 
1. Examples: Sniper, Simpoints, Smarts
2. Accurate in overall ipc measurement
3. 
### Behaviour-domain 
1. Examples: HRD, mocktails, WEST (memory domain)
2. Preserve average behavior, Faster than time-domain method (1/10 synthesis ratio)
3. Can not model phase transition (ipc curve, memory access patterns)
## "Time-domain" -> "Freq-domain" -> "Time-domain"
### Necessity Analysis
### Sufficient Analysis
### Global Stable Load Instruction
>- ***Some loads repeatedly fetch the same data value from same load address across entire workload***
Both operations, address generation & data fetch, produce identical results across all dynamic instances
Prime targets for breaking data dependency without execution

> - ***Why do these loads even exist in well-optimized real-world workloads?***
Accessing global-scope variables
Accessing local variables of inline functions
Limited set of architectural registers

> - ***A dynamic load instance I2 of a static load instruction I is bound to fetch the same value from the same memory location as the previous dynamic instance I1 of the same static load instruction when the following two conditions are satisfied.*** 
	> 	-  Condition 1: None of the source registers of I has been written between the occurrences of I1 and I2. 
	> 	-  Condition 2: No store or store request has arrived to the memory address of I1 between the occurrences of I1 and I2. 

> Satisfying Condition 1 ensures that I2 would have the same load address as I1, and thus the address computation operation of I2 can be safely eliminated. Satisfying Condition 2 ensures that I2 would fetch the same value from the memory as I1, and thus the data fetch operation of I2 can be safely eliminated.
### Value leaking as Addresses
> - Values are data that should not be used, directly or indirectly, as memory addresses (e.g. password hashes, private encryption keys). A value can leak as a memory address when there is information flow from the value to a memory address. The scope of the tool is limited to detecting data-flow: it tracks data dependences but disregards control dependences. Assume secret is a value, the leakage in the code in Fig. 3.1 will not be detected by the tool. In Fig. 3.2, the tool will detect the leakage because secret is involved in the computation of &A[i]. Furthermore, addr will be tagged as a leak point. A leak point is a memory location where a leaked value resides.
```
secret = *addr;
if (secret % 2)
    a = A[0];
else
    a = A [128];
```
Fig. 3.1. Code that leaks a value via control-flow.

```
secret = *addr;
i += 64 * secret;
a = A[i];
```
Fig. 3.2. Code that leaks a value via data-flow. 
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTEyMTI0NTU0MywyMDI1MTA5MjA0LC00Nz
g5NzAsMTA0NzYyMzQzMSw5MjM2NTAzNjgsLTEwNDM5OTQ1NjNd
fQ==
-->