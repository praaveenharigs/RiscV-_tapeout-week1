
# Day 1: Introduction to Verilog RTL Design & Synthesis
#### This document covers the complete workflow of a 2:1 Multiplexer using Verilog in Yosys and th SkyWater 130nm standard cell library.
---
### *SKY130RTL D1SK1 L1 Introduction to iverilog design and test bench*
### Simulator
A simulator is a piece of software that uses test inputs and outputs to verify that your digital circuit is working properly. This aids in design verification prior to hardware implementation.
- One tool for simulating your design is a simulator.
- We are utilizing the open-source simulator Iverilog on Day 1.
<img width="1823" height="862" alt="Screenshot 2025-09-23 112627" src="https://github.com/user-attachments/assets/abd58bb5-e55c-4159-96de-5f0f69059aa9" />

The stimulator is searching for a change in the input data.
The vcd file is the stimulator's output.

### Design 
- Design is the set of verilog codes which has the intended functionality to meet the specific requirements.

### Test Bench 
- It is setup to apply stimulus to the design to check its functionality.
- Test Bench doesn't have a Primary input or outputs.
- Note, only the design has its Primary input or outputs.
   <img width="1637" height="773" alt="Screenshot 2025-09-23 112012" src="https://github.com/user-attachments/assets/2760af3b-d030-48c1-ba2b-aa7d6d72f9e7" />

### GTKWave
- It is used for viewing the waveform.

---
### SKY130RTL D1SK2 L1 Lab1 introduction to lab
For cloning the git https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git

```bash
sudo -i
sudo apt-get install git
cd /home
# for the vbox users
cd vboxuser 
cd vsd
cd VLSI
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop
cd verilog_files/
ls
```

## After  cloning the git

Use iverilog to compile design and test bench files.
```bash
mkdir -p ~/vcd/photos

```
## Run the Simulation

```bash
iverilog -o ~/vcd/photos/good_mux_sim.vvp good_mux.v tb_good_mux.v
vvp good_mux_sim.vvp 
```

### Open the Waveform using GTKWave

```bash
gtkwave tb_good_mux.vcd
```
**Waveform**

<img width="1718" height="819" alt="image" src="https://github.com/user-attachments/assets/de31ea66-d7ba-4db4-a39f-22b5747205e5" />


To install gvim:
``` bash
apt install vim-gtk3 -y
```
If you want to change the desgin
```bash
gvim tb_good_mux.v -o good_mux.v
```

---

### Verilog Code Analysis

**The code for the multiplexer (`good_mux.v`):**

```verilog
module good_mux (input i0, input i1, input sel, output reg y);
always @ (*)
begin
    if(sel)
        y <= i1;
    else 
        y <= i0;
end
endmodule
```

###  **How It Works**

**Inputs:**
- Data Inputs: `i0`, `i1`
- Selection Line: `sel`

**Registered Output:** `y`

**Logic:**
  - If `sel`= 1, `y`=`i1`
  - If `sel`= 0, `y`=`i0`.

---
## **Introduction to Yosys**
 - Yosys is a powerful open-source synthesis tool for digital hardware. 
 - It takes your Verilog code and converts it into a gate-level netlist.
 - The set of primary inputs and outputs of the design code should be same.
 - Verification of the netlist file can be done by generating gtkwave for netlist file.
 - The same TB can be used in verification process.
<img width="1202" height="335" alt="image" src="https://github.com/user-attachments/assets/474e9689-f92b-4234-8a79-444eef05c397" />

### What is .lib
- Collection of standard logic modules.
- It consists of all the logic gates with the variation in inputs given to it.
- It has same gates with different flavors.
- We need cells that work fast to meet the specific performance and also cells that work slow to meet HOLD.
---

## Synthesis Lab with Yosys

Let’s synthesize the `good_mux` design!

###  Step-by-Step Yosys Flow

1. **Start Yosys**
```shell
yosys
```
2. **Read the liberty library**
```shell
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
3. **Read the Verilog code**
```shell
read_verilog good_mux.v
```
4.  **Synthesis of the design**
```shell
synth -top good_mux
```
5.  **Map to Technology File**
```bash
dfflibmap -liberty ~/vsd/VLSI/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ~/vsd/VLSI/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
6. **Visualize the Netlist**
```shell
show
```
7. **Write the Gate-Level Netlist**

Copy: `good_mux_netlist.v` and paste it in `home`
```shell
write_verilog ~/good_mux_netlist.v
```
8. **Generate Report**
```bash
stat
```
---
### **Synthesis Outputs:**
#### Netlist Dot File:
<img width="1718" height="801" alt="image" src="https://github.com/user-attachments/assets/dcd2acba-bccd-4a84-ba1d-1bb065bd8407" />


#### Statistics:

<img width="1280" height="830" alt="image" src="https://github.com/user-attachments/assets/b0ae6558-ab6d-4dfa-b489-bce8be882273" />



---
### Summary:
- You learned about testbench, design and stimulator.
- You analyzed the 2:1 Multiplexer.
- You explored Yosys, iverilog, and GTKWave. 
## Run the Simulation

```bash
