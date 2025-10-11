# 📌 Combinational Logic Optimization

Combinational circuits → outputs depend only on present inputs.

## Techniques

- Constant Propagation.
- Boolean Logic Optimisation.

`opt_clean -purge` is an optimization pass in Yosys that removes unused or redundant logic.

It scans the design and eliminates:

- Unused wires.

- Unconnected ports.

- Dangling logic cells (logic that doesn’t affect outputs).

  #### NOTE: PMOS Transistor stacking increases resistance way more than the NMOS Transistor stacking which leads to:
  #### - Degraded Performance
  #### - Slower Switching
  #### So it is more preferred to use NAND gates than NOR gates in logic implementation and synthesis.

### Examples

#### Opt_check

<img width="941" height="139" alt="opt check1to3" src="https://github.com/user-attachments/assets/77b0a3ed-9313-4336-87d4-4f8ca705e445" />


```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v
synth -top opt_check
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```


<img width="1206" height="297" alt="combinational opt lab1 and usingmux" src="https://github.com/user-attachments/assets/6db95829-e0d8-46b7-947c-7523879ee087" />


__ The verilog code which implements a MUX is optimised into an 2-input AND gate logic after netlist synthesis. __

     
#### Opt_check2


```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check2.v
synth -top opt_check2
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```


<img width="1206" height="297" alt="comb opt or with mux" src="https://github.com/user-attachments/assets/e5cd25c6-a8f8-42a9-b7de-6535b951ae20" />


   __ The verilog code which implements a MUX is optimised into an 2- input OR gate logic after netlist synthesis. __


#### Opt_check3


```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check3.v
synth -top opt_check3
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```


<img width="1206" height="439" alt="comb optcheck3 3input and" src="https://github.com/user-attachments/assets/a7faa89e-a7e5-4072-b111-b4ab3e97cbbb" />


__ The verilog code which implements a cascading of Multiplexers is optimised into an 3-input AND gate logic after netlist synthesis. __


### Task

#### 1.Opt_check4


<img width="491" height="79" alt="comb optcheck4 verilog code" src="https://github.com/user-attachments/assets/e3f992b1-a80f-451b-8354-267c2a07f2c0" />



```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check4.v
synth -top opt_check4
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```


<img width="600" height="195" alt="comb optcheck4 final" src="https://github.com/user-attachments/assets/b2c86862-ed41-45f2-aacd-f240b556f999" />


##### 🔎Observation


- The variable `b` vanishes completely ➡️ It has no influence.
- The circuit simplifies drastically from a 3-variable to a 2-variable function.
- Function is XNOR ➡️ output is 1 when `a`=`c`.


#### 2.Multiple_modules


<img width="624" height="363" alt="comb opt multiplemoduleopt verilog code" src="https://github.com/user-attachments/assets/8610d842-63d7-4e80-836a-10f4282fc027" />

First we will flatten the design using `flatten` command and then synthesize the optimised netlist using `opt_clean -purge` command.

```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_module_opt.v
synth -top
flatten
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```


<img width="1203" height="628" alt="comb opt multiplemoduleopt" src="https://github.com/user-attachments/assets/3e12c589-d5b6-47c0-aef3-7c6a1b3ef966" />


##### 🔎Observation

- The variable `d` vanishes completely ➡️ It has no influence.
- The circuit simplifies drastically from a 4-variable to a 3-variable function.


#### 3.Multiple_module_opt2



<img width="648" height="287" alt="multiple module opt2 verilog code" src="https://github.com/user-attachments/assets/b95786a1-e547-406a-98d5-16883d2d8f51" />

We will again flatten the RTL code and generate a optimised netlist.


```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_module_opt2.v
synth -top
flatten
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```


<img width="419" height="566" alt="multiple module opt 2 output" src="https://github.com/user-attachments/assets/6fee91cc-9b97-4d2b-bcc9-8816a24d0c55" />


##### 🔎Observation 
- All inputs become irrelevant. Variables  do not affect the output at all.

- Logic elimination: All intermediate AND gates can be removed and the output tied to constant 0 (GND).

- Synthesis impact: A synthesizer will constant-propagate and replace the logic with a constant net — this saves area, delay, and dynamic power.

- Power/area optimization: Remove fanout and upstream logic if those outputs are unused elsewhere — further savings may be possible.

- No hazards/glitches: The output is statically 0, so there are no dynamic hazards on this signal itself (though removing logic might affect timing elsewhere).



# 📌 Sequential Logic Optimization

Sequential circuits → outputs depends on present inputs as well as on the previous Outputs.

## Techniques

- Basic
   - Sequential Constant Propagation
- Advanced
   - State Optimisation-Reducing the number of states in a sequential circuit’s FSM without changing its external behavior.
       - Equivalent states (that produce the same outputs and have the same transitions) are merged.
       - This reduces the number of flip-flops and combinational logic needed to implement the FSM.
   - Cloning-Duplicating a register (flip-flop) so that multiple fanout paths each get their own copy of the register.
       - Used when one register drives many logic blocks and the fanout load causes delay, routing congestion, or power issues.
       - By creating clones (copies) of that register, the fanout is split and each path is driven independently.
   - Retiming-Moving flip-flops (registers) across combinational logic to balance delays without changing the functionality of the circuit.
       - Goal: minimize the critical path delay → improve maximum clock frequency.
       - Secondary goals: sometimes reduce area or power.

### Examples

#### dff_const

<img width="477" height="490" alt="dff_count1 and2 verilog code" src="https://github.com/user-attachments/assets/1bd3b231-1795-4725-8bdf-eaa274ce1e53" />


```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const.v
synth -top dff_const
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```


<img width="1207" height="300" alt="dff check1 output" src="https://github.com/user-attachments/assets/c6db147f-8e6d-4939-a2b0-b306b53b38f3" />

##### 🔎 Observation
- After optimisation the gate level synthesis contain a D-Flip Flop and a reset input.


#### dff_const2


```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const2.v
synth -top dff_const2
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

<img width="612" height="491" alt="dff count2 output" src="https://github.com/user-attachments/assets/bbb1e170-3b0f-4181-a2ba-ac4038f14863" />


##### 🔎 Observation

- Output is constant

   - Since both conditions (reset=1 and reset=0) drive q <= 1, the output never changes.
   - Functionally, it is equivalent to tying Q permanently to logic 1.


- Inputs are redundant

  - clk and reset have no effect on the final output.
  - The D input is not even present/used.


- Degenerated flip-flop

   - It looks like a D flip-flop with async reset, but after simplification it loses its flip-flop behavior.
   - Synthesis will optimize away the flop and replace it with a constant net tied to VDD


- Hardware implication

  - In real hardware, this design will just map to a logic 1 connection (a tie-high cell) instead of using sequential logic.


#### dff_const3


<img width="477" height="329" alt="dff count 3 verilog code" src="https://github.com/user-attachments/assets/1a6d10e0-6316-466a-9c83-14c682ccb803" />



```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const3.v
synth -top dff_const3
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```


<img width="1201" height="292" alt="dff count3 output" src="https://github.com/user-attachments/assets/cb850ded-1a20-4492-94c0-82e2f1492c77" />


##### 🔎 Observation

- The generated netlist is a casacading of two D-Flip Flops with the same `reset` and `clock`.
- The output q1 is connected to the input of the flip flop with output q.

### Task


#### 1. dff_const4


<img width="477" height="329" alt="dff const4 verilog code" src="https://github.com/user-attachments/assets/89e2fd97-c12b-4497-aaaa-713bacb0055a" />



```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const4.v
synth -top dff_const4
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

<img width="601" height="540" alt="dff const4 output" src="https://github.com/user-attachments/assets/4052dc3b-2cca-405f-b57c-1a944df83dac" />



##### 🔎 Observation

- It looks like a cascading of two D flip-flop with async reset, but after simplification it loses its flip-flop behavior.
- Since both conditions (reset=1 and reset=0) drive q <= 1, the output never changes.
- Synthesis will optimize away the flops and replace them with a constant net tied to VDD.
- clk and reset have no effect on the final output.


#### 2. dff_const5



<img width="477" height="329" alt="dff const5 verilog code" src="https://github.com/user-attachments/assets/556c54e6-7077-465c-91fd-4e6ffb3dcb78" />


```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const5.v
synth -top dff_const5
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```


<img width="1195" height="264" alt="dff const5 output" src="https://github.com/user-attachments/assets/58acdc64-8a1f-465a-8f18-2cc1bc4a0928" />



##### 🔎 Observation

- The generated netlist is a cascading of two-D-Flip Flops with 1 and q1 as inputs and the final output is q.
- Both the flip flops contain the same `reset` and `clock`.
- The output will change based on the `reset` and the inputs to the flops.






















































 
