
# Day 4: Gate-Level Simulation (GLS), Blocking vs. Non-Blocking in Verilog, and Synthesis-Simulation Mismatch

Welcome to Day 4 of the RTL Workshop!

1. **Gate-Level Simulation (GLS)**
2. **Blocking vs. Non-Blocking Assignments in Verilog**
3. **Synthesis-Simulation Mismatch**

---
## Table of Contents

[1. Gate Level Simulation (GLS)](#1-gate-level-simulation-gls)
   - GLS Workflow in iverilog.
   - Why and When to perform GLS
   - Challenges

[2. Synthesis-Simulation Mismatch](#2-synthesis-simulation-mismatch)
[3. Blocking vs. Non-Blocking Assignments in Verilog](#3-blocking-vs-non-blocking-assignments-in-verilog)
   - Blocking Statements
   - Non-Blocking Statements

[4. Labs](#4-labs)

[5. Summary](#5-summary)





## 1. Gate-Level Simulation (GLS)

**GLS** stands for **Gate-Level Simulation**.  Gate level simulation (GLS) is a technique for verifying the functionality and timing of a digital circuit after it has been synthesized from a high-level description.  The RTL code and the generated netlist are logically equivalent (well, supposed to be!) and hence the same testbenches can be used to verify both. It is a verification step in the VLSI design flow where the synthesized gate-level netlist of a digital circuit is simulated to validate:

- Functional correctness
- Timing behavior
- Power estimates
- Test structures (e.g., scan chains for DFT)

### Why Perform GLS?

- **Synthesis Validation**: Ensures that synthesis tools faithfully translate RTL into gates.
- **Timing Verification**: Simulates with realistic delays (from SDF files), allowing you to check for timing violations.
- **Testability**: Confirms that scan chains and other test features work post-synthesis.

### When is GLS Performed?

- **After synthesis**: Once the RTL is converted into a gate-level netlist.
- **Before physical design**: To catch issues early, before layout.

### Types of GLS

- **Functional GLS**: Logic-only simulation, often with zero or unit delays.
- **Timing GLS**: Uses annotated timing data to check real-world timing behavior.

### GLS using iverilog:

<img width="1905" height="960" alt="Screenshot 2025-09-27 000524" src="https://github.com/user-attachments/assets/ffc36cd4-4a42-497b-96d3-0794f0c9b741" />

### Challenges:
- GLS simulations are inherently more complex than RTL simulations due to the detailed representation of gates and their interactions. This complexity results in longer simulation times and higher memory requirements.

- Modern logic simulators only update the state of the design when an event occurs (e.g., clock edge or input change). This can make the simulations slower and may take hours to finish depending on design complexity.

- Debugging issues in GLS can be challenging due to potential initialization problems and X propagation (unknown states).

---

### Synthesis Simulation Mismatch:
- Misssing sensitivity list
   - As we know that simulator works based on the activity.
   - A **synthesis-simulation mismatch** occurs when the simulation results of RTL (pre-synthesis) do not match simulation results of the gate-level netlist (post-synthesis) or hardware. Reasons include:
        - Non-synthesizable constructs: Use of delays, initial blocks, or other code not supported by synthesis.
        - Incomplete or ambiguous coding: E.g., missing `else` clauses, improper sensitivity lists.
        - Tool interpretation differences: Simulation and synthesis tools may interpret ambiguous RTL differently.

   - Always write synthesizable, unambiguous RTL and follow good coding practices to minimize mismatches.

- Blocking Vs. Non-blocking assignments
    - Inside always block
       - `=`: Blocking assignment
          - executes the statements in the order it is written
          - So the first statement is evaluated before the second statement.
          - **Execution:** Sequential, executes immediately.
          - **Suitable for:** Combinational logic (e.g., `always @(*)`).
          - **Example:**  
           ```verilog
            always @(*)
                y = a & b;
           ```
       - `<=`: Non-blocking assignment
          - Parallel evaluation.
          - Executes all the RHS when always block is entered and assigns to LHS.
          - **Execution:** Scheduled, executes concurrently at the end of the time step.
          - **Suitable for:** Sequential logic (e.g., `always @(posedge clk)`).
          - **Example:**  
          ```verilog
            always @(posedge clk)
            q <= d;
          ```
---

## Labs

### Lab 1

```bash
iverilog -o ~/vcd/a.out ternary_operator_mux.v tb_ternary_operator_mux.v
```
then,
```bash
cd ~/vcd
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
<img width="1235" height="793" alt="image" src="https://github.com/user-attachments/assets/a8b68920-84e7-4def-998d-5724cd9bc329" />


To view the verilog file 
```bash
gedit ternary_operator_mux.v
```
<img width="1021" height="532" alt="image" src="https://github.com/user-attachments/assets/42f87165-4213-415d-8c7b-e1d955425746" />

#### Enter into the yosys by running it in the verilog_files directory:
```bash
cd ~/vsd/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files
yosys
```
Inside yosys, run
```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ternary_operator_mux.v
synth -top  ternary_operator_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="979" height="622" alt="image" src="https://github.com/user-attachments/assets/60b09a44-1e80-47ec-9d80-9c5d5ade3fa9" />

---
## GLS of Ternary Operator MUX
Gate-Level Simulation is performed using the synthesized netlist (ternary_operator_mux_net.v). This helps verify the functional correctness of the design after synthesis, using the actual standard cells and any delays (if modeled)

```bash
iverilog -o ~/vsd/a.out ../my_lib/verilog_model/primitives.v  ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
cd ~vsd
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
<img width="1237" height="792" alt="image" src="https://github.com/user-attachments/assets/0705b270-3dda-4cee-8e84-e793223503dd" />

---

## Lab 2

```bash
iverilog -o ~/vcd/a.out bad_mux.v tb_bad_mux.v
```
then,
```bash
cd ~/vcd
./a.out
gtkwave tb_bad_mux.vcd
```
<img width="1226" height="792" alt="image" src="https://github.com/user-attachments/assets/4fda3dc1-fa11-4845-bd46-33d00df764e8" />

To view the verilog file 
```bash
gedit bad_mux.v
```
<img width="1022" height="542" alt="image" src="https://github.com/user-attachments/assets/848c6036-3cba-4a20-86e8-2ffbfe3bf361" />

#### Enter into the yosys by running it in the verilog_files directory:
```bash
cd ~/vsd/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files
yosys
```
Inside yosys, run
```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog bad_mux.v
synth -top  bad_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="932" height="497" alt="image" src="https://github.com/user-attachments/assets/facde03d-65a4-4d57-8c73-2531ac1a07b4" />

---
## GLS of Ternary Operator MUX
Gate-Level Simulation is performed using the synthesized netlist (bad_mux_net.v). This helps verify the functional correctness of the design after synthesis, using the actual standard cells and any delays (if modeled)

```bash
iverilog -o ~/vsd/a.out ../my_lib/verilog_model/primitives.v  ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_mux_net.v tb_bad_mux.v
cd ~/vsd
./a.out
gtkwave tb_bad_mux.vcd
```
<img width="1240" height="762" alt="image" src="https://github.com/user-attachments/assets/538246f0-2f05-4bd2-a998-672a86e21924" />

---

### Lab 3

```bash
iverilog -o ~/vcd/a.out blocking_caveat.v tb_blocking_caveat.v
```
then,
```bash
cd ~/vcd
./a.out
gtkwave tb_blocking_caveat.vcd
```

<img width="1262" height="431" alt="image" src="https://github.com/user-attachments/assets/3523107a-86c4-4aa4-a3c0-e96c91863592" />

To view the verilog file 
```bash
gedit blocking_caveat.v
```
<img width="1020" height="528" alt="image" src="https://github.com/user-attachments/assets/0980fb84-1ccb-4be0-90ec-42284d4806c3" />


#### Enter into the yosys by running it in the verilog_files directory:
```bash
cd ~/vsd/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files
yosys
```
Inside yosys, run
```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog blocking_caveat.v
synth -top  blocking_caveat
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="948" height="574" alt="image" src="https://github.com/user-attachments/assets/cc8b80e3-4259-47b3-b0c4-6c643ab4ac15" />

---
## GLS of Blocking Caveat Mux
Gate-Level Simulation is performed using the synthesized netlist (blocking_caveat_mux_net.v). This helps verify the functional correctness of the design after synthesis, using the actual standard cells and any delays (if modeled)

```bash
iverilog -o ~/vsd/a.out ../my_lib/verilog_model/primitives.v  ../my_lib/verilog_model/sky130_fd_sc_hd.v blocking_caveat_net.v tb_blocking_caveat.v
cd ~/vsd
./a.out
gtkwave tb_blocking_caveat.vcd
```

<img width="1262" height="499" alt="image" src="https://github.com/user-attachments/assets/1cc7f786-d2c5-4182-aadb-41213dbb8226" />

---

## Summary

- Gate-Level Simulation (GLS): Validates netlist functionality, timing, and testability after synthesis.
- Synthesis-Simulation Mismatch: Avoid by using synthesizable RTL code.
- Blocking vs. Non-Blocking: Use blocking (=) for combinational, non-blocking (<=) for sequential logic.

