- 
## Definition of *Global Stable Loads*
- ***A dynamic load instance I2 of a static load instruction I is bound to fetch the same value from the same memory location as the previous dynamic instance I1 of the same static load instruction when the following two conditions are satisfied.*** 
	> 	-  Condition 1: None of the source registers of I has been written between the occurrences of I1 and I2. 
	> 	-  Condition 2: No store or store request has arrived to the memory address of I1 between the occurrences of I1 and I2. 

> Satisfying Condition 1 ensures that I2 would have the same load address as I1, and thus the address computation operation of I2 can be safely eliminated. Satisfying Condition 2 ensures that I2 would fetch the same value from the memory as I1, and thus the data fetch operation of I2 can be safely eliminated.

### Correction of implementation

```cpp
if(last occurrence.find(address) == last occurrence.end()) {
	last occurrence[address] = i; 
} else {
	// Conditions are evaluated here
	// ... 
	last occurrence[address]= i;
}
```
### Extending the concpets 
Store Instructions

Definition: A store instruction is globally stable if it writes the same value to the same memory location across dynamic instances when its inputs have not changed.

Conditions for Store Stability:

Condition 1: The source registers and memory operands providing the store address and data have not been modified since the last occurrence.
Condition 2: No other instruction has modified the target memory location since the last store.
Implementation Implications:

Tracking Inputs and Outputs: Monitor the source registers for both the address and data, as well as the memory location being written.
Memory State Consistency: Ensure that no intervening stores have modified the memory location.
Simulation Optimization: If conditions are satisfied, the store can be considered redundant and potentially eliminated in simulation, reducing memory operation overhead.

##### However, it turned that *global stable stores* defined like the above only consist  of ~0.6% total instructions from the traces, compared to *global stable loads* that typically consist of  ~10-20%, *global stable stores* are negligible.
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDk3ODU3NjM2LC03NDUwMDc4MDMsLTE1MD
MwNjY1OThdfQ==
-->