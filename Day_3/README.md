

# Day 3: Combinational and Sequential Optimization

Welcome to Day 3 of this workshop! Today we discuss optimization of combinational and sequential circuits, introducing techniques to enhance efficiency and performance.

---

## Table of Contents

- [Combinational Logic Optimization](#Combinational-Logic-Optimization)
- [Sequential Logic Optimization](#Sequential-Logic-Optimization)
- [Combinational Logic Optimization labs](#Combinational-Logic-Optimization-labs)
- [Sequential Logic Optimization labs](#Sequential-Logic-Optimization-labs)
---

## Combinational Logic Optimization

### Objective:
- Squeeze and simplify logic to get the most optimized design in terms of area and power.

### Techniques:

- #### Constant Propagation:
   - Replaces logic blocks with constants when input values are fixed.
   - - **Example:**
    ```
    Y = ((A·B) + C)' 
    If A = 0 → Y = (0 + C)' = C'
    ```
  - **Result:**
   - Complex gate logic (6 MOS transistors) simplifies to a single inverter (2 MOS transistors).
- #### Boolean Logic Optimization:
   - Simplify Boolean expressions using: Karnaugh Map (K-Map) and Quine McCluskey Method.
   - **Example Verilog:**
    ```verilog
    assign y = a ? (b ? c : (c ? a : 0)) : (!c);
    ```
    
    - Optimized expression:  
      ```
      y = a ⊕ c
      ```
      
### Yosys Optimization Commands:
  
  - The command to perform logic optimization in Yosys is opt_clean.
    ```bash
       opt_clean
    ```
  - Additionally, for a hierarchical design involving multiple sub-modules, the design must be flattened by running the flatten command before executing the opt_clean command.
     ```bash
        flatten
     ```
     then,
    ```bash
        synth -top <module_name>
        opt_clean -purge
    ```

---

## Constant Propagation  
### 1. What is it?  
- Replace signals driven by **constant values (0 or 1)** with fixed logic.  
- Eliminates unnecessary gates and wires.  

### 2. Detailed Info  
Constant propagation checks if a net or signal always evaluates to a fixed value and simplifies the circuit accordingly. For example, if a gate input is always `0`, the output can be optimized to `0` without the actual logic.  

### Advantages  
- Reduces **gate count**  
- Simplifies **logic equations**  
- Improves **area and power efficiency**  

---

## State Optimization  
### 1. What is it?  
- Reduces the number of **states in a finite state machine (FSM)** by merging equivalent or unreachable states.  

### 2. Detailed Info  
In FSMs, some states may be redundant (behaving the same as others) or unreachable. State optimization identifies and removes them, leading to a minimal FSM.  

###  Advantages  
- Reduces **flip-flop count**  
- Improves **timing** (fewer state transitions)  
- Lower **area and power** usage  

---

## Cloning  
### 1. What is it?  
- Duplicate logic or registers to **reduce fanout** and **improve timing**.  

### 2. Detailed Info  
If a single gate or flip-flop drives many loads, the delay increases. Cloning creates a copy of that logic/register so loads are split between them.  

###  Advantages  
- Reduces **net delay** from high fanout  
- Improves **timing closure**  
- Helps achieve **higher operating frequency**

## Retiming  
### 1. What is it?  
- **Reposition registers (flip-flops)** in a circuit without changing functionality.  

### 2. Detailed Info  
Retiming balances combinational delays by moving flip-flops across logic gates. It helps meet setup/hold timing while preserving overall input-output behavior.  

###  Advantages  
- Optimizes **critical path delay**  
- Enables **higher clock frequency**  
- Balances **pipeline stages** for better performance

---
## Sequential Logic Optimization

#### Basic:
- **Sequential Constant Propagation**  
  - Propagates known constant values through flip-flops during synthesis.

### Basic Techniques:

#### 1.Sequential Constant Propagation:

- Propagates known constant values through flip-flops during synthesis.

 **Advanced Techniques:**
 
 #### 1. State Optimization

- Reduces the number of FSM states → optimizing area and transitions.

 #### 2. Retiming

Moves registers across logic boundaries to balance delay and improve timing.

 #### 3. Sequential Logic Cloning

- Duplicates logic in floorplan-aware synthesis to meet timing and congestion goals.

### Outcome:
- These optimization techniques ensure faster, smaller, and power-efficient circuits, ready for real-world chip design.

---
### **To get start through the optimization of design logic**

**Go through the step by step worflow**

#### Locate the file in your project directory. 

```bash
cd ~/vsd/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files
```
#### List the optimization designs
 ```bash
 ls *opt*
```

#### list the sequential optimization designs
 ```bash
 ls *dff*
```
---

# Combinational Logic Optimization labs

### Lab 1 :

To see the verilog logic of Lab1.
```bash
gedit opt_check.v
```

<img width="1141" height="764" alt="image" src="https://github.com/user-attachments/assets/f20b400c-604f-4be3-82cb-c2d604cf3fa0" />


#### Explanation:

- assign y = a ? b : 0; means:
- If a is true, y is assigned the value of b.
- If a is false, y is 0.

#### Lanch yosys:
```bash
yosys
```

#### 6. **Run this command inside yosys:**
```bash
read_liberty -lib ~/vsd/VLSI/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v
synth -top opt_check
opt_clean -purge
abc -liberty ~/vsd/VLSI/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

<img width="1115" height="673" alt="image" src="https://github.com/user-attachments/assets/8c0e2625-dc85-4d55-930c-df897c86ef88" />


#### Netlist Dot File

<img width="1072" height="635" alt="image" src="https://github.com/user-attachments/assets/8e54044b-312e-4af6-a486-66019577f964" />


### Statistics
```bash
=== opt_check ===

   Number of wires:                  3
   Number of wire bits:              3
   Number of public wires:           3
   Number of public wire bits:       3
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:                  1
     $_AND_                          1
```
then, exit from yosys
```bash
exit
```

---
### Lab 2:
To see the verilog logic of Lab 2.
```bash
gedit opt_check2.v
```

<img width="1149" height="394" alt="image" src="https://github.com/user-attachments/assets/28e2bf27-1740-4eeb-bbff-702c7ad0c297" />


#### Code analysis:
1. if a is true then the output is 1.
2. else 0.

#### launch yosys:
```bash
yosys
```
Inside yosys:
```bash
read_liberty -lib ~/vsd/VLSI/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check2.v
synth -top opt_check2
opt_clean -purge
abc -liberty ~/vsd/VLSI/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

<img width="1224" height="563" alt="image" src="https://github.com/user-attachments/assets/bec7e309-84be-475f-a9ce-264d842e5195" />



#### Netlist Dot File 
<img width="979" height="571" alt="image" src="https://github.com/user-attachments/assets/376eff94-b855-475e-b17c-bb3ba3f67e7e" />


#### Statistics
```bash
=== opt_check2 ===

   Number of wires:                  3
   Number of wire bits:              3
   Number of public wires:           3
   Number of public wire bits:       3
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:                  1
     $_OR_                           1
```
then, exit from yosys
```bash
exit
```

---
### Lab 3
To see the verilog logic of Lab3.
```bash
gedit opt_check3.v
```

<img width="1144" height="541" alt="image" src="https://github.com/user-attachments/assets/68d87cc4-44ad-4244-82c3-0b896c2a522e" />


#### Code explanation
- if c is true, then the output is b, else 0.
- if c is b then the value of y be b or 0 depends on the value of a.

Enter inside the yosys by the command:
```bash
yosys
```
Inside yosys
```bash
read_liberty -lib ~/vsd/VLSI/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check3.v
synth -top opt_check3
opt_clean -purge
abc -liberty ~/vsd/VLSI/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="1220" height="552" alt="image" src="https://github.com/user-attachments/assets/de306491-0c79-4d66-ba6a-e2f1f113959f" />


### Netlist Dot File
<img width="1021" height="506" alt="image" src="https://github.com/user-attachments/assets/98068b27-ba2a-4485-a69c-a3137fe7b197" />


#### Statistics:
```bash
=== opt_check3 ===

   Number of wires:                  5
   Number of wire bits:              5
   Number of public wires:           4
   Number of public wire bits:       4
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:                  2
     $_ANDNOT_                       1
     $_NAND_                         1
```
---

# Sequential Logic Optimization labs

### Lab 4
To see the logic of verilog code for lab 4.
```bash
gedit dff_const1.v
```


#### Code Analysis
- At positive edge of the clock:
  - if reset is high, then the value of q is 0.
  - if reset is low, then the value of q is 1.

To go inside the yosys, run
```bash
yosys
```
then, Inside yosys
```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
synth -top dff_const1
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
### Netlist Dot File

<img width="1261" height="620" alt="image" src="https://github.com/user-attachments/assets/92147286-496b-4cf5-b40e-622dbea02c51" />


---
## Lab 5
To see the logiv verilog file, run this
```bash
gvim dff_const2.v
```

### Code logic
- In all positive edge of reset and clock, the output q will high always.
- q=1
Enter into the yosys
```bash
yosys
```
Inside the yosys, run
```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const2.v
synth -top dff_const2
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

#### Netlist Dot File
<img width="1030" height="541" alt="image" src="https://github.com/user-attachments/assets/16a8fea3-b636-4b3f-860c-2026ca5b3055" />


---
### Lab 6
To see the logic of the verilog file
```bash

```


Entering into the yosys
```bash
yosys
```

Inside yosys
```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const3.v
synth -top dff_const3
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="1256" height="620" alt="image" src="https://github.com/user-attachments/assets/15f27261-f023-455f-ab14-003d11a14381" />

---
## Summary
### Combinational Optimization Techniques:
  - Constant Propagation: Replaces logic with constants → fewer gates, less power.
  - Boolean Logic Optimization: Uses K-Map / Quine-McCluskey to simplify expressions.
   
### Sequential Optimization Techniques:
    - State Optimization: Minimizes FSM states → reduces flip-flops and transitions.
    - Retiming: Moves registers to balance delay → improves timing.
    - Cloning: Duplicates logic/registers to reduce fanout → helps timing closure.

### Yosys Commands Used:
   - flatten → Converts hierarchical design into flat design.
   - opt_clean -purge → Removes unused nets and redundant logic.
   - dfflibmap / abc → Map logic to technology libraries for synthesis.

### Lab Outcomes:
   - Optimized designs show reduced transistor count, logic levels, and area.
   - Some flip-flops collapsed into constants → saving resources.
   -  Visualized dot files confirm simplifications in synthesized netlists.

By applying these optimizations, designs become smaller, faster, and more power-efficient, ready for real-world VLSI implementation.

