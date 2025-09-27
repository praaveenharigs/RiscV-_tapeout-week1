

# Digital Synthesis with Yosys

## Table of Contents
1. Understanding the Library
2. PVT Corners
3. Types of Synthesis 
   - Hierarchical Synthesis
   - Flat Synthesis
4. Submodule-Level Synthesis
5. DFF with Asynchronous Reset

---

## Understanding the Library

The standard cell library used in synthesis is:  

```

sky130_fd_sc_hd__tt_025C_1v80.lib

```

### Breakdown of the Name
- **sky130_fd_sc_hd** => SkyWater 130nm, high-density, standard cell.  
 - **tt** →  *Typical process corner*: typical behavior of transistors.  
 - **025C** →  characterized by an operating temperature of **25°C**.  
 - **1v80** →  characterized by a supply voltage of **1.80 V**.  

 Details about each cell are provided by this `.lib` file, including: - Logic functionality (e.g., AND, OR, Flip-Flop).  
 - Timing features (setup/hold times, delays).  
 The amount of power used.  
 The footprint of an area.  

 In order to produce a **gate-level netlist** that accurately represents actual hardware, Yosys projects the RTL design onto these cells during synthesis.

---

## PVT Corners

**PVT = Process, Voltage, Temperature** — the three key parameters that affect circuit behavior:  

**Procedure (P):** Takes into consideration manufacturing variances (slow, typical, and fast transistors).  
 **Voltage (V):** Variations in supply voltage (e.g., 1.62 V to 1.98 V near nominal 1.80 V).  
 - **Temperature (T):** From low (like -40°C) to high (like 125°C), this device can operate.  

 To make sure that designs satisfy **timing, power, and reliability requirements** in all real-world scenarios, they must be validated across several PVT corners.
### Example PVT Corners

| Corner | Process | Voltage | Temperature | Usage |
|--------|---------|---------|-------------|-------|
| **FF** | Fast    | 1.98 V  | -40°C       | Best-case delay (fastest) |
| **TT** | Typical | 1.80 V  | 25°C        | Nominal operating point |
| **SS** | Slow    | 1.62 V  | 125°C       | Worst-case delay (slowest) |

---



## Types of Synthesis
There are two types of synthesis approaches in Yosys:
1. **Hierarchical Synthesis**  
2. **Flat Synthesis**



## Hierarchical Synthesis
**Explanation:**
- Preserves the module hierarchy (each sub-module remains separate).  
- Makes debugging and analysis easier.  
- Useful for modular design and incremental development.  
- Optimizations happen within each module independently.  

### Commands:
```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v 
synth -top multiple_modules
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show multiple_modules
write_verilog multiple_modules_hier.v 
write_verilog -noattr multiple_modules_hier.v 
````

<img width="1443" height="740" alt="image" src="https://github.com/user-attachments/assets/8dc296a1-bf2e-4519-9396-a7a000a1aa7e" />

---

## Flat Synthesis

**Explanation:**

* Flattens the hierarchy into a single-level netlist.
* Provides better **global optimization** across modules.
* Less readable, but efficient for final design stages.
* Often used before tape-out for best performance.

### Commands:

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top multiple_modules
flatten
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog multiple_modules_flat.v
write_verilog -noattr multiple_modules_flat.v
```

<img width="1444" height="641" alt="image" src="https://github.com/user-attachments/assets/d48675b1-ec24-4a93-a360-779985fc69eb" />


---




## Submodule-Level Synthesis

Occasionally, we might want to synthesize individual **submodules** rather than the entire top-level design.  
 This method works well when:  

 Full synthesis is sluggish or resource-intensive due to the **large top-level design**.  
 In the top design, a **submodule is instantiated several times**.  Redundant work is avoided by reusing its netlist and synthesising it only once.  
 Because each block can be independently checked, it enables **modular development and debugging**.  
 The final design can then be created by combining all of the synthesized submodule netlists. 

---



### Example: Synthesizing `sub_module1`

**Commands:**
```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top sub_module1
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show sub_module1
write_verilog sub_module1_netlist.v
write_verilog -noattr sub_module1_netlist.v
````

## **DFF with Asynchronous Reset**

### **Verilog Code**

```verilog
module dff_asyncres (
    input clk,
    input async_reset,
    input d,
    output reg q
);
always @ (posedge clk, posedge async_reset) begin
    if (async_reset)
        q <= 1'b0;
    else    
        q <= d;
end
endmodule
```

**Explanation:**

* `clk` → Rising edge triggers data capture.
* `async_reset` → Immediately resets `q` to `0` when high, independent of the clock.
* `d` → Data input.
* `q` → Output of the flip-flop.

Asynchronous reset **has higher priority** than the clock edge.

---



### **Synthesis Commands**

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_asyncres.v
synth -top dff_asyncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog dff_asyncres_net.v
write_verilog -noattr dff_asyncres_net.v
```

---

### **Why We Use `dfflibmap`**

* Maps **generic Yosys flip-flops** to **library-specific DFFs** available in `sky130_fd_sc_hd__tt_025C_1v80.lib`.
* Ensures DFFs meet **timing, area, and power constraints**.
* Produces a **technology-mapped netlist** ready for place-and-route tools.

Without `dfflibmap`, the synthesized DFF would remain **generic** and not optimized for the target library.

---

### Waveform 

After synthesis, simulate `dff_asyncres_net.v` to check:

* `q` follows `d` on **rising clock edges**.
* `q` resets **immediately** when `async_reset` is high.

This confirms that the **hardware-mapped DFF behaves correctly**.

``` bash
iverilog dff_asyncres.v tb_dff_asyncres.v
./a.out
gtkwave tb_dff_asyncres.vcd
````

---

### **Other DFF Variants**

The **same synthesis flow** applies to:

| DFF Variant    | Description                |
| -------------- | -------------------------- |
| `dff_syncres`  | DFF with synchronous reset |
| `dff_asyncset` | DFF with asynchronous set  |
| `dff_syncset`  | DFF with synchronous set   |

**Only the Verilog module name changes**; commands and flow remain the same.

---
## Summary

- **Timing Libraries:**  
  - `.lib` files (e.g., `sky130_fd_sc_hd__tt_025C_1v80.lib`) define standard cell timing and characteristics used for accurate synthesis and timing analysis.

- **Synthesis Approaches:**  
  - **Hierarchical Synthesis:** Synthesizes sub-modules individually; allows better optimization and area control.  
  - **Flat Synthesis:** Synthesizes the entire design at once; simpler but may miss local optimization opportunities.  
  - Flattening can be done post-hierarchical synthesis if needed.

- **Efficient Flip-Flop Coding Styles:**  
  - **Asynchronous Reset/Set:**
    - Immediate response
    - Independent of clock
    - Useful for global initialization.  
  - **Synchronous Reset/Set:**
    - Changes occur only on clock edge
    - Ensures predictable timing and avoids hazards.  

- **Simulation & Synthesis Workflow:**  
  - **Simulation:** `iverilog` → `vvp` → `GTKWave`.  
  - **Synthesis (Yosys):** Load library → read Verilog → `synth` → `dfflibmap` → `abc` → `show`.

