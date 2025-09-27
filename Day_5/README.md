

# Day 5: Optimization in Synthesis

Welcome to Day 5 of the RTL workshop! Today, we will cover optimization in Verilog synthesis, focusing on `if-else statements`, `for` loops, generate blocks, and explore how improper coding can lead to inferred latches. Labs are included for hands-on experience.

---

## Content

- [1. If-Else Statements in Verilog](#1-if-else-statements-in-verilog)
- [2. Inferred Latches in Verilog](#2-inferred-latches-in-verilog)
- [3. Labs for If-Else and Case Statements](#3-labs-for-if-else-and-case-statements)
  - [Lab 1: Incomplete If Statement](#lab-1-incomplete-if-statement)
  - [Lab 2: Synthesis Result of Lab 1](#lab-2-synthesis-result-of-lab-1)
  - [Lab 3: Nested If-Else](#lab-3-nested-if-else)
  - [Lab 4: Synthesis Result of Lab 3](#lab-4-synthesis-result-of-lab-3)
  - [Lab 5: Complete Case Statement](#lab-5-complete-case-statement)
  - [Lab 6: Synthesis Result of Lab 5](#lab-6-synthesis-result-of-lab-5)
  - [Lab 7: Incomplete Case Handling](#lab-7-incomplete-case-handling)
  - [Lab 8: Partial Assignments in Case](#lab-8-partial-assignments-in-case)
- [4. For Loops in Verilog](#4-for-loops-in-verilog)
- [5. Generate Blocks in Verilog](#5-generate-blocks-in-verilog)
- [6. What is an RCA (Ripple Carry Adder)?](#6-what-is-an-rca-ripple-carry-adder)
- [7. Labs on Loops and Generate Blocks](#7-labs-on-loops-and-generate-blocks)
  - [Lab 9: 4-to-1 MUX Using For Loop](#lab-9-4-to-1-mux-using-for-loop)
  - [Lab 10: 8-to-1 Demux Using Case](#lab-10-8-to-1-demux-using-case)
  - [Lab 11: 8-to-1 Demux Using For Loop](#lab-11-8-to-1-demux-using-for-loop)
  - [Lab 12: 8-bit Ripple Carry Adder with Generate Block](#lab-12-8-bit-ripple-carry-adder-with-generate-block)
- [Summary](#summary)

---

## 1. If-Else Statements in Verilog

- **If-else statements** are used for conditional execution in behavioral modeling, typically within procedural blocks (`always`, `initial`, tasks, or functions).
- Mostly used for priority logic.

### 2. Inferred Latches:
- Inferred Latches in `if` is due to the incomplete `if` statement.
- Due to incomplete `if` statement, the incomplete condition will get latched with either of inputs and with the output.
- Until it is intented to do the incomplete `if-else` statements.
   - For example,
         - The verilog code for the counter is also an incomplete `if-else` statement but it's logic doesn't affected by this and it is intended to have it in the code.
     ```verilog
             module counter (
                   input clk, 
                   input rst, 
                   input enable,
                   output reg [2:0] count
              );

            always @(posedge clk or posedge rst) begin
            if (rst)
             count <= 3'd0;
           else if (enable)
              count <= count + 1;
           end

           endmodule
      ```
### Syntax

```verilog
if (condition) begin
    // Code for true Condition
end else begin
    // Code for false Condition
end
```

### Nested If-Else

```verilog
if (condition1) begin
    // Code for condition1 true
end else if (condition2) begin
    // Code for condition2 true
end else begin
    // Code if all Conditions are false
end
```

---

### Case Statement 
The `if-else` and `case` statement should be in the `always` block and the variable declared in inside it should be `reg <variable>` .

#### Caveats with case statement
- Due to the incomplete `case` statement, it will become inferred latch .
- Due to this, the incomplete case statements is latched with the output.
- To avoid this, code `case` with default case statement.

```verilog
case (expression)
    value1 : begin
                 // statements for value1
             end
    value2 : begin
                 // statements for value2
             end
    ...
    default : begin
                 // statements if none of the above match
              end
endcase
```

#### Solution: Add Else or Default Case
```verilog
module ex (
    input wire a, b, sel,
    output reg y
);
    always @(a, b, sel) begin
        case(sel)
            1'b1 : y = a;
            default : y = 1'b0; // Default assignment
        endcase
    end
endmodule
```



---

## 3. Labs for If-Else and Case Statements

### Lab 1: Incomplete If Statement

The incomplete if verilog code is 

<img width="1019" height="290" alt="image" src="https://github.com/user-attachments/assets/ec35dd5c-28e7-45f5-a924-21b1d34d567f" />

The gtkwave is:

<img width="1239" height="789" alt="image" src="https://github.com/user-attachments/assets/b4d9b9f8-3943-4c1b-9a71-4dd889187473" />

The Netlist Dot File:

<img width="1012" height="602" alt="image" src="https://github.com/user-attachments/assets/232c7a0b-1121-4469-ace9-2511f45ac6ae" />

---

### Lab 2: Nested If-Else
The incomplete nested if-else verilog code is

<img width="894" height="493" alt="image" src="https://github.com/user-attachments/assets/2b31560f-b129-40eb-9e90-b164b2cfb5c4" />

gtkwave is:

<img width="1249" height="785" alt="image" src="https://github.com/user-attachments/assets/64ded2d1-dd4c-43b3-8997-47397224cfa8" />

Netlist Dot File is:

<img width="928" height="498" alt="image" src="https://github.com/user-attachments/assets/4fdf2a6f-bb96-4cc9-a7c3-beba9893976b" />

---


### Lab 3: Complete Case Statement
The complete case statement verilog code of comp_case is

<img width="880" height="454" alt="image" src="https://github.com/user-attachments/assets/cbe2c620-fe9c-4a24-bf77-7f08ec5ee6c6" />

gtkwave is:

<img width="1245" height="785" alt="image" src="https://github.com/user-attachments/assets/303a58e0-4840-42a1-881d-7c4cbe89c2ec" />

Netlist Dot File is:

<img width="1111" height="588" alt="image" src="https://github.com/user-attachments/assets/126786be-c44d-4c56-8612-4baac15e24a5" />


### Lab 4: Incomplete Case Handling

The bad_case verilog code is

<img width="889" height="379" alt="image" src="https://github.com/user-attachments/assets/829f0699-ac8b-42fe-8344-a1f97e95462c" />

gtkwave is:

<img width="1268" height="669" alt="image" src="https://github.com/user-attachments/assets/681fecdf-4552-42a0-a158-98966dfe1eed" />

Netlist Dot File is:

<img width="917" height="689" alt="image" src="https://github.com/user-attachments/assets/15200838-1a30-4600-ac5d-dcfe8a467b7a" />

---
## 4. For Loops in Verilog

A **for loop** is used within procedural blocks (`initial`, `always`, tasks/functions) to execute statements multiple times based on a loop counter.

### Syntax

```verilog
for (initialization; condition; increment) begin
    // Statements to execute
end
```

- Must be inside procedural blocks.
- Synthesizable only if the number of iterations is fixed at compile time.

#### Example: 4-to-1 MUX Using a For Loop

```verilog
module mux_4to1_for_loop (
    input wire [3:0] data, // 4 input lines
    input wire [1:0] sel,  // 2-bit select
    output reg y           // Output
);
    integer i;
    always @(data, sel) begin
        y = 1'b0; // Default output
        for (i = 0; i < 4; i = i + 1) begin
            if (i == sel)
                y = data[i];
        end
    end
endmodule
```

---

## 5. Generate Blocks in Verilog

A **generate block** is used to create hardware structures such as module instances or logic at compile time. Typically used with `for` loops and the `genvar` keyword.

#### Example

```verilog
genvar i;
generate
    for (i = 0; i < 4; i = i + 1) begin : gen_loop
        and_gate and_inst (.a(in[i]), .b(in[i+1]), .y(out[i]));
    end
endgenerate
```

---

## 6. What is an RCA (Ripple Carry Adder)?

An RCA adds binary numbers using a chain of full adders. To add `n` bits, you need `n` full adders. Each carry-out connects to the carry-in of the next stage.

<img width="971" height="408" alt="image" src="https://github.com/user-attachments/assets/78b61798-de6a-4434-a446-7fd8014fbf83" />

---

## 7. Labs on Loops and Generate Blocks

### Lab 9: 4-to-1 MUX Using For Loop

```verilog
module mux_generate (
    input i0, input i1, input i2, input i3,
    input [1:0] sel,
    output reg y
);
wire [3:0] i_int;
assign i_int = {i3, i2, i1, i0};
integer k;
always @(*) begin
    for (k = 0; k < 4; k = k + 1) begin
        if (k == sel)
            y = i_int[k];
    end
end
endmodule
```
<img width="1832" height="902" alt="image" src="https://github.com/user-attachments/assets/c7d72bdb-bb67-4c8d-9ca9-0783efaaa90b" />

---

### Lab 10: 8-to-1 Demux Using Case

```verilog
module demux_case (
    output o0, output o1, output o2, output o3,
    output o4, output o5, output o6, output o7,
    input [2:0] sel,
    input i
);
reg [7:0] y_int;
assign {o7, o6, o5, o4, o3, o2, o1, o0} = y_int;
always @(*) begin
    y_int = 8'b0;
    case(sel)
        3'b000 : y_int[0] = i;
        3'b001 : y_int[1] = i;
        3'b010 : y_int[2] = i;
        3'b011 : y_int[3] = i;
        3'b100 : y_int[4] = i;
        3'b101 : y_int[5] = i;
        3'b110 : y_int[6] = i;
        3'b111 : y_int[7] = i;
    endcase
end
endmodule
```
<img width="1829" height="900" alt="image" src="https://github.com/user-attachments/assets/f846b157-238b-4b82-a4a5-b994e487499f" />

---

### Lab 11: 8-to-1 Demux Using For Loop

```verilog
module demux_generate (
    output o0, output o1, output o2, output o3,
    output o4, output o5, output o6, output o7,
    input [2:0] sel,
    input i
);
reg [7:0] y_int;
assign {o7, o6, o5, o4, o3, o2, o1, o0} = y_int;
integer k;
always @(*) begin
    y_int = 8'b0;
    for (k = 0; k < 8; k = k + 1) begin
        if (k == sel)
            y_int[k] = i;
    end
end
endmodule
```
<img width="1832" height="897" alt="image" src="https://github.com/user-attachments/assets/d1c0c926-617a-449c-a338-99261576d8fb" />

---

### Lab 12: 8-bit Ripple Carry Adder with Generate Block

```verilog
module rca (
    input [7:0] num1,
    input [7:0] num2,
    output [8:0] sum
);
wire [7:0] int_sum;
wire [7:0] int_co;

genvar i;
generate
    for (i = 1; i < 8; i = i + 1) begin
        fa u_fa_1 (.a(num1[i]), .b(num2[i]), .c(int_co[i-1]), .co(int_co[i]), .sum(int_sum[i]));
    end
endgenerate

fa u_fa_0 (.a(num1[0]), .b(num2[0]), .c(1'b0), .co(int_co[0]), .sum(int_sum[0]));

assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule
```
**Full Adder Module:**
```verilog
module fa (input a, input b, input c, output co, output sum);
    assign {co, sum} = a + b + c;
endmodule
```
<img width="1264" height="620" alt="image" src="https://github.com/user-attachments/assets/ef8be5c5-1c4d-4f7a-964a-8c1bc52bbc4a" />

---

---

## Summary

- Use complete if-else and case statements to avoid unintended latch inference.
- For loops and generate blocks are powerful for writing scalable, synthesizable code.
- Always ensure every signal is assigned in every possible execution path for combinational logic.
- Use labs to reinforce concepts with practical Verilog code and synthesis results.

---

