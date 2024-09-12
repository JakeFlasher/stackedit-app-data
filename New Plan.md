## For the first time, introduce the concept of "time-domain" and "freq-domain" in workload synthesis
### Time-domain 
1. Examples: Sniper, Simpoints, Smarts
2. Accurate in overall ipc measurement
3. 
### Frequency-domain 
1. Examples: HRD, mocktails, WEST
2. Preserve average behavior, Faster than time-domain method (1/10 synthesis ratio)
3. Can not model phase transition (ipc curve, memory access patterns)
## "Time-domain" -> "Freq-domain" -> "Time-domain"
### Necessity Analysis
### Sufficient Analysis
### Global Stable Load Instruction

### Value leaking as Addresses
Values are data that should not be used, directly or indirectly, as memory addresses (e.g. password hashes, private encryption keys). A value can leak as a memory address when there is information flow from the value to a memory address. The scope of the tool is limited to detecting data-flow: it tracks data dependences but disregards control dependences. Assume secret is a value, the leakage in the code in Fig. 3.1 will not be detected by the tool. In Fig. 3.2, the tool will detect the leakage because secret is involved in the computation of &A[i]. Furthermore, addr will be tagged as a leak point. A leak point is a memory location where a leaked value resides.
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
> Written with [StackEdit中文版](https://stackedit.cn/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjAwNTU0NTM5MCw5MjM2NTAzNjgsLTEwND
M5OTQ1NjNdfQ==
-->