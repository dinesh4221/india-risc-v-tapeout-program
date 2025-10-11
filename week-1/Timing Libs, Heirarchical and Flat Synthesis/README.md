# 📌Timing libs

Timing libraries (also called technology libraries or simply libs) are essential files that describe the electrical characteristics and timing behavior of standard cells like gates, flip-flops, multiplexers, etc.

A `.lib` file includes:

### 1. Cell Definitions:

Each standard cell (like NAND2X1, DFF, etc.) is defined with:

- Area

- Pins (input/output/inout)

- Function (Boolean logic)

### 2. Timing Arcs:

Define how long it takes a signal to propagate from input to output, depending on:

- Input slew (how fast signal changes)

- Output load (capacitance driven)

### 3. Power Models:

- Dynamic power (due to switching)

- Leakage power (static power)

### 4. Constraints:

-Setup and hold times for flip-flops/latches

-Minimum pulse width, clock-to-q delay, etc.

🏗️ Timing Libraries are essential for:

- Synthesis: Mapping RTL to gates (e.g., synth in Yosys or Synopsys Design Compiler)

- Static Timing Analysis (STA): Verifying your design meets timing (e.g., with PrimeTime, OpenSTA)

- Power Estimation: Estimating total power consumption
  
- Place & Route (P&R): Optimizing placement and wire delay based on cell timing

# 📌Heirarchical vs Flat Synthesis

|Feature|Flat Synthesis|Heirarchical Synthesis|
|-------|--------------|----------------------|
|Definition| Entire design is synthesized as a single unit| Each module is synthesized separately|
|Structure|	Design is flattened into one big module|	Design preserves hierarchy|
|Optimization scope|	Global across whole design|	Local within each module|
|Memory/Compute use|	High (entire design in memory)|	Lower (works module by module)|
|Runtime|	Slower for large designs|	Faster, scalable for large designs|
|Resulting Area/Timing|	Often better optimized|	Slightly less optimized (due to local view)|
|Debugging|	Harder (no hierarchy retained)|	Easier (preserves module boundaries)|
|Design reuse	| Hard| Easy (modules are separate)|

## <int>**Heirarchical Synthesis**</int>



<img width="620" height="256" alt="Screenshot from 2025-09-27 01-22-43" src="https://github.com/user-attachments/assets/c897ec04-4a21-4c95-b525-68209da3706b" />


### Top module 
```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show multiple_modules
write_verilog -noattr multiple_modules_heir.v
!gvim multiple_modules_heir.v
```



<img width="611" height="250" alt="heirarchical design" src="https://github.com/user-attachments/assets/b9a1acd7-9e3f-49c9-866b-bb6e01d1be05" />  


### Sub-Module
```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -submodule1
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show multiple_modules
```


 
<img width="1212" height="733" alt="submodule1" src="https://github.com/user-attachments/assets/bbed8d17-a551-4e83-8b6c-a4ed5cd8852a" />



## <int>**Flat Synthesis**</int>
In Yosys, the `flatten` command is used to flatten the hierarchy of the design — that is, to recursively replace module instances with their definitions, resulting in a single-level, flat design.

```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top
flatten
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show multiple_modules
write_verilog -noattr multiple_modules_flat.v
!gvim multiple_modules_flat.v
```


<img width="1212" height="733" alt="flattened multiple modules" src="https://github.com/user-attachments/assets/a8af33d5-610a-4ed5-b595-e5dc6769e4e2" />

## 🧠 What is Module-Level Synthesis?

Instead of synthesizing your entire chip/design at once, you synthesize each module separately, then combine them later. This keeps your design hierarchy intact during synthesis.
It provides:
- Scalability for Large Designs.
- Parallelism and Faster Turnaround.
- Preserves Hierarchy for Debugging and Analysis.
- Avoid Over-Optimization Risks.


# 📌Efficient Flop Coding Styles

## Asynchronous Set and Reset

The set (S) and reset (R) inputs take effect immediately, regardless of the clock.

They override the clock and data input.

Useful for fast response to initialization or emergency conditions.

- Timing:

  - Changes on S or R don’t wait for the clock edge.

  - Can cause metastability or glitches if not handled carefully.


## Synchronous Set and Reset

Set and reset inputs are sampled on clock edges.

They take effect only at the clock edge, just like D input.

Usually implemented as control signals in the always @(posedge clk) block.

- Timing:

  - No immediate override — set/reset happen in sync with clock.

  - Easier to analyze and integrate with synchronous logic. 

### Asynchronus Reset D-Flip Flop

<img width="644" height="189" alt="dff async reset verilog code" src="https://github.com/user-attachments/assets/ded5e1ef-3320-4fde-b4f7-b07e8441ebb1" />


#### 📊Waveform


<img width="1212" height="421" alt="asynchronus reset d gtkwave" src="https://github.com/user-attachments/assets/396b8c97-d898-409b-ae3f-a6902bf7ce45" />



The `dfflibmap` command is used to map generic D-type flip-flops in your design to technology-specific flip-flops defined in a standard cell library.



```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_asyncres.v
synth -top dff_asyncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```


<img width="1212" height="270" alt="dff async reset block" src="https://github.com/user-attachments/assets/5880d6d3-d570-448d-8f90-694f981ee977" />




### Asynchronus Set D-Flip Flop



<img width="644" height="221" alt="asynchronus set dff verilog code" src="https://github.com/user-attachments/assets/bf5628c8-6138-47a2-9d7f-d359bfefa840" />


#### 📊Waveform


<img width="1212" height="421" alt="asynchronus set d gtkwave" src="https://github.com/user-attachments/assets/fde36c4f-209d-4506-8939-a6bb5106e3ee" />



```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_async_set.v
synth -top dff_async_set
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```


<img width="1212" height="270" alt="dff async set block" src="https://github.com/user-attachments/assets/46010e11-1e30-430c-8a99-a918963a567e" />


### Synchronus Reset D-Flip Flop


<img width="644" height="221" alt="sync reset verilog code" src="https://github.com/user-attachments/assets/9b165890-08d6-4680-a2e1-1d30c4cbd4af" />


#### 📊Waveform



<img width="1212" height="270" alt="synchronus reset d gtkwave" src="https://github.com/user-attachments/assets/5308efd0-f385-47bc-b29a-e31b710b0cdb" />



```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_syncres.v
synth -top dff_syncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```



<img width="1212" height="270" alt="dff sync reset block" src="https://github.com/user-attachments/assets/2648e778-d4ed-4e69-9b81-5ff68b588ba4" />




# 📌Optimisation

In digital VLSI design flow, optimization is the stage (or set of steps) where the design is refined to meet performance, power, and area (PPA) requirements while still being functionally correct.

## 🔹 Types of Optimization in Digital Design

- Timing Optimization

   - Reduce critical path delay to meet clock frequency.
   - Techniques: gate sizing, buffer insertion, retiming, pipelining.

- Area Optimization

   - Reduce chip silicon footprint.
   - Techniques: logic sharing, removing redundant gates, using smaller cells.

- Power Optimization

   - Minimize dynamic and static power.
   - Techniques: clock gating, multi-Vt cells, power gating, low-power libraries.

- Interconnect Optimization

   - Reduce wire delay & congestion.
   - Techniques: layer assignment, buffer insertion, routing optimization.

## 👉 Example


<img width="681" height="222" alt="mul2 and 8 verilog codes" src="https://github.com/user-attachments/assets/e51ea419-b804-4c2b-a0eb-c87e0394a0cc" />

Two verilog codes which performs multiplication of a input by 2 and 8.

### Multipled by 2

```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog mult_2.v
synth -top mul2
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```



<img width="1212" height="325" alt="optimisation1(a0)" src="https://github.com/user-attachments/assets/0c8c9f86-83d3-488b-b123-88b8109b9bbb" />


#### Generating Netlist File


```bash
write_verilog mul2_net.v
!gvim mul2_net.v
```



<img width="422" height="219" alt="mul2 netlist" src="https://github.com/user-attachments/assets/75f154a4-5ca7-4aef-93a4-362c222d65bf" />



#### 🔎 Observation

The output netlist file doesn't implement a multiplier instead it shifts the input to the left by 1 bit(as multiplication of a binary number by 2 shifts the number by 1-bit).



### Multiplied by 8


```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog mult_8.v
synth -top mult_8
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```


<img width="1212" height="325" alt="optimisation mult8" src="https://github.com/user-attachments/assets/34616ba4-424a-450d-9870-8936934868d2" />



#### Generating Netlist File


```bash
write_verilog mul2_net.v
!gvim mult_8_net.v
```


<img width="637" height="142" alt="mult8 netlist" src="https://github.com/user-attachments/assets/77d7d9b8-9ce1-4e08-ad22-d58cdcc573df" />


#### 🔎 Observation


The bits of the input(binary) are shifted by 3-bits instead implementing a multiplier which performs the multiplication of the input with 8.



























