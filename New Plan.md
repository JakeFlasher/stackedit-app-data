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


**Extending the Concept of Global Stable Instructions to Other Instruction Types**

---

**1. Reviewing the Concept of Global Stable Load Instructions**

**Global Stable Load Instructions** are load instructions that, under certain conditions, repeatedly fetch the same value from the same memory address across different dynamic instances. According to the Constable paper, the conditions are:

- **Condition 1**: None of the source registers of the load instruction have been modified between its consecutive dynamic instances.
- **Condition 2**: The memory address from which the load fetches data has not been modified by any store or invalidated by any snoop request between these instances.

When these conditions are satisfied, both the address computation and data fetch operations of the load instruction yield the same results across dynamic instances. This allows for safely eliminating the load instruction's execution, improving performance without affecting correctness.

**2. Formulating a Higher-Level Abstract Concept**

To extend this concept to other instruction types, let's define a more general concept:

**Global Stable Instructions** are instructions that, under specific conditions, consistently produce the same output (result or side effect) when executed, across different dynamic instances. This implies that their execution can be safely eliminated or simplified in simulation or optimization without affecting the correctness of the program.

**General Conditions for Global Stability**:

- **Condition A**: The inputs (source registers and memory operands) to the instruction have not changed since the last occurrence.
- **Condition B**: The instruction's output or side effects are not influenced by external events or state changes that have occurred since its last execution.

By ensuring these conditions, we can identify instructions that can be candidates for elimination or simplification, potentially accelerating simulations or optimizing performance.

**3. Extending to Other Instruction Types**

Let's apply this concept to branches, stores, and other instructions.

---

**a. Branch Instructions**

**Definition**: A branch instruction is globally stable if it consistently takes the same path (i.e., always taken or always not taken) across dynamic instances when the inputs determining the branch condition have not changed.

**Conditions for Branch Stability**:

- **Condition 1**: None of the source registers or memory locations used in the branch condition have been modified since the last occurrence of the branch.
- **Condition 2**: No external events or interrupts have occurred that could affect the branch outcome.

**Implementation Implications**:

- **Tracking Source Operands**: Monitor the source registers and memory operands that influence the branch condition.
- **Outcome Consistency**: Check if the branch outcome remains the same across instances when the inputs have not changed.
- **Simulation Optimization**: If the conditions are met, we can assume the same branch outcome, potentially bypassing detailed branch prediction modeling in simulations and reducing execution overhead.

---

**b. Store Instructions**

**Definition**: A store instruction is globally stable if it writes the same value to the same memory location across dynamic instances when its inputs have not changed.

**Conditions for Store Stability**:

- **Condition 1**: The source registers and memory operands providing the store address and data have not been modified since the last occurrence.
- **Condition 2**: No other instruction has modified the target memory location since the last store.

**Implementation Implications**:

- **Tracking Inputs and Outputs**: Monitor the source registers for both the address and data, as well as the memory location being written.
- **Memory State Consistency**: Ensure that no intervening stores have modified the memory location.
- **Simulation Optimization**: If conditions are satisfied, the store can be considered redundant and potentially eliminated in simulation, reducing memory operation overhead.

---

**c. Other Instruction Types (Arithmetic, Logic, etc.)**

**Definition**: An instruction is globally stable if it produces the same output when the inputs have not changed, and it has no side effects influenced by external state.

**Conditions for Instruction Stability**:

- **Condition 1**: Source operands (registers and memory operands) have not changed since the last execution.
- **Condition 2**: Instruction does not involve operations with side effects like modifying system state, I/O operations, or relying on dynamic data (e.g., time, random numbers).

**Implementation Implications**:

- **Result Caching**: Cache the results of such instructions and reuse them when conditions are met.
- **Simulation Optimization**: Skip redundant execution of these instructions, thereby reducing computational overhead in simulations.

---

**4. Checking Correctness and Availability**

**Correctness Assurance**:

- **Strict Condition Enforcement**: Only eliminate or simplify instructions when the defined conditions are strictly met to ensure program correctness.
- **State Monitoring**: Continuously monitor changes to inputs (register modifications, memory writes) and external events.
- **Fallback Mechanism**: In cases of uncertainty or when conditions are invalidated, default to normal instruction execution.

**Availability Analysis**:

- **Workload Characteristics**: The frequency and distribution of globally stable instructions depend on the program's behavior and workload patterns.
- **Profiling**: Perform analysis on target workloads to identify opportunities where instructions frequently meet stability conditions.
- **Compiler Influence**: The extent to which globally stable instructions are present may be affected by compiler optimizations.

---

**5. Implementation Strategy**

**Common Implementation Components**:

- **Instruction Tracking Tables**: Similar to Constable's Stable Load Detector (SLD), use dedicated tables for each instruction type to track stability.
- **Input Monitoring Structures**:

  - **Register Monitor Table (RMT)**: Track modifications to source registers.
  - **Memory Monitor Table (MMT)**: Track modifications to memory operands.

- **Confidence Mechanism**: Use confidence counters to ensure that an instruction consistently meets stability conditions before considering it globally stable.

---

**a. Implementing Global Stability for Branch Instructions**

- **Branch Stability Table (BST)**:

  - **Indexed** by branch instruction PC.
  - **Stores**:

    - Last branch outcome (taken/not taken).
    - Confidence counter.
    - Source operands.

- **Algorithm**:

  1. **At Execution Time**:

     - Fetch the branch instruction.
     - Check if the source operands have been modified since the last occurrence.
     - If not, compare the current branch outcome with the last outcome.
     - Update confidence counter accordingly.

  2. **Determining Stability**:

     - If confidence exceeds a predefined threshold, mark the branch as globally stable.
     - In simulation, assume the same branch outcome, potentially bypassing detailed execution.

  3. **Handling Modifications**:

     - If any source operand is modified, reset the confidence counter.
     - Monitor for external events that could affect the branch outcome.

- **Potential Benefits**:

  - Reduce branch misprediction penalties in simulation.
  - Simplify control flow modeling for stable branches.

---

**b. Implementing Global Stability for Store Instructions**

- **Store Stability Table (SST)**:

  - **Indexed** by store instruction PC.
  - **Stores**:

    - Last store address.
    - Last store value.
    - Confidence counter.
    - Source operands.

- **Algorithm**:

  1. **At Execution Time**:

     - Fetch the store instruction.
     - Check if source operands (address and data) have not changed since the last occurrence.
     - Verify that the target memory location has not been modified by other instructions.

  2. **Determining Stability**:

     - If confidence exceeds the threshold, mark the store as globally stable.
     - In simulation, consider eliminating the store or simplifying its effects.

  3. **Handling Modifications**:

     - If any source operand changes or the memory location is modified, reset the confidence counter.
     - Ensure memory consistency is maintained.

- **Potential Benefits**:

  - Reduce memory write operations in simulation.
  - Simplify memory state modeling for stable stores.

---

**c. General Implementation Steps**

- **Initialize Tracking Structures**: Set up tables for each instruction type at the start of simulation.

- **Instruction Processing Loop**:

  - For each instruction in the trace:

    1. **Fetch and Decode**: Identify instruction type and relevant operands.

    2. **Check Stability Conditions**:

       - Use tracking tables to determine if conditions are met.
       - Update confidence counters based on outcomes.

    3. **Decision Making**:

       - If an instruction is deemed globally stable:

         - **For Simulation**: Skip detailed execution, assume known results.
         - **For Optimization**: Consider eliminating the instruction or simplifying its execution.

    4. **State Updates**:

       - Track modifications to registers and memory.
       - Update tracking tables with new information.

- **Logging**:

  - Record instruction counts or identifiers of globally stable instructions.
  - Store this information in an offline file for analysis or further processing.

---

**6. Example Pseudocode**

Here's an illustrative pseudocode for extending the implementation to branch instructions:

```cpp
// Data structures
struct BranchInfo {
    size_t last_occurrence;
    bool last_outcome;
    size_t confidence;
};

std::unordered_map<uint64_t, BranchInfo> branch_table; // Key: Branch instruction PC
std::unordered_map<unsigned, size_t> last_write_to_reg; // Key: Register ID

// Simulation loop
for (size_t i = 0; i < nsimulate; ++i) {
    auto input_ins = reader.read_single_instr();
    const auto &decoded_instr = decoder.decode(input_ins);

    if (decoded_instr.op == propagator::instr::opcode::OP_BRANCH) {
        auto ip = decoded_instr.ip;
        auto &branch_info = branch_table[ip];
        bool inputs_unchanged = true;

        // Check if source registers have been written since last occurrence
        for (const auto &reg : decoded_instr.src_reg) {
            if (last_write_to_reg.find(reg) != last_write_to_reg.end() &&
                last_write_to_reg[reg] > branch_info.last_occurrence) {
                inputs_unchanged = false;
                break;
            }
        }

        if (inputs_unchanged) {
            // Compare current outcome with last outcome
            if (branch_info.last_outcome == decoded_instr.branch_taken) {
                branch_info.confidence++;
            } else {
                branch_info.confidence = 0; // Reset confidence
            }

            // Update last outcome
            branch_info.last_outcome = decoded_instr.branch_taken;

            // Determine if branch is globally stable
            if (branch_info.confidence >= CONFIDENCE_THRESHOLD) {
                // Log the instruction count or ID
                stable_branches_file.write(reinterpret_cast<const char *>(&i), sizeof(i));
                // In simulation, assume the same branch outcome
                continue; // Skip detailed execution
            }
        } else {
            // Inputs have changed; reset confidence
            branch_info.confidence = 0;
            branch_info.last_outcome = decoded_instr.branch_taken;
        }

        branch_info.last_occurrence = i;
    }

    // Update last writes to registers
    for (const auto &reg : decoded_instr.dst_reg) {
        last_write_to_reg[reg] = i;
    }

    // Handle other instruction types similarly...
}
```

**Notes**:

- **CONFIDENCE_THRESHOLD** is a predefined value that determines how many times the branch must exhibit the same behavior before being considered globally stable.
- Similar logic can be applied to store instructions and other types, adapting the data structures and conditions accordingly.

---

**7. Conclusion**

By extending the concept of global stable instructions to branches, stores, and other instruction types, we can identify and eliminate redundant instruction executions in simulations. This can lead to significant simulation time speedups while maintaining the overall performance metrics and correctness.

Implementing this requires careful tracking of instruction inputs (registers, memory addresses) and state modifications. Using confidence mechanisms ensures that only instructions exhibiting consistent behavior are considered globally stable.

By leveraging existing codebases like `constable.cc` and the Clueless project, you can adapt and extend the implementations to handle additional instruction types, enhancing your research and simulation capabilities.

**Recommendations**:

- **Validate on Diverse Workloads**: Test the extended implementation across various workloads to evaluate effectiveness and gather insights.
- **Adjust Confidence Thresholds**: Fine-tune the confidence thresholds based on empirical data to balance between eliminating redundant instructions and maintaining correctness.
- **Monitor Impact on Metrics**: Continuously monitor performance metrics to ensure that eliminating instructions does not adversely affect simulation accuracy.

---

I hope this helps you in extending your research and achieving further simulation speedups while preserving performance metrics. If you have any further questions or need clarification on any part of the implementation, feel free to ask!
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTc4NzIwMDkxOSwxMTIxMjQ1NTQzLDIwMj
UxMDkyMDQsLTQ3ODk3MCwxMDQ3NjIzNDMxLDkyMzY1MDM2OCwt
MTA0Mzk5NDU2M119
-->