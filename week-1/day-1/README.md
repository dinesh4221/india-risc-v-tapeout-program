# 📌VERILOG RTL DESIGN AND SYNTHESIS
This file demonstrates a simple RTL design, simulation and synthesis flow using:

- **Icarus Verilog** for simulation
- **GTKWave** for waveform viewing
- **Yosys** for RTL synthesis
- **ABC** (invoked via Yosys) for technology mapping / optimization

It includes example RTL, a testbench that generates a VCD, and a Yosys script for synthesis.

A testbench is a non-synthesizable Verilog code written to verify the behavior of a design (RTL module).
It does not become hardware — instead, it is used only for simulation.

First clone the Github repository of the Workshop
```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop
cd sky130RTLDesignAndSynthesisWorkshop
```
The repository contains the verilog codes and Testbenches for the Upcoming labs.
## MUX Design and Synthesis

### 🛠️ Workflow:

👉 Compile your design:
```bash
iverilog good_mux.v tb_good_mux.v
```
<img width="985" height="574" alt="design and testbench of mux" src="https://github.com/user-attachments/assets/e2cec704-4149-401d-bf6f-707f43fc8af2" />

👉 Run the simulation:
```bash
./a.out
```

👉 Visualize signals using GTKWAVE:
```bash
gtkwave tb_good_mux.vcd
```
<img width="1204" height="711" alt="gtkwave of mux" src="https://github.com/user-attachments/assets/24fdc460-0423-4024-b390-2ba8c36b4801" />


### Gate level Netlist Synthesis using YOSYS

👉 The `read_liberty` command is used to read a standard cell library file (`.lib`) into Yosys.

👉 The `read_verilog` command tells Yosys to read your RTL code written in Verilog and import it into its internal database.

👉 The `synth -top` command is used to synthesize a Verilog design and specify which module is the top-level module of your design hierarchy.

👉 The `abc -liberty` command is used to map your synthesized design to a gate-level netlist using ABC, a logic synthesis and verification tool, with a provided Liberty (`.lib`) file that defines the standard cell library.

👉 `write_verilog` is a command used to export your synthesized or processed design into a Verilog netlist

👉 `gvim` command is used of viewing and editing source code.

```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog good_mux.v
synth -top good_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr good_mux_netlist.v
!gvim good_mux_netlist.v
```
<img width="612" height="555" alt="mux" src="https://github.com/user-attachments/assets/af3cc347-f344-4f7f-94f7-3790d479af0b" />

<img width="971" height="576" alt="netlist file of mux" src="https://github.com/user-attachments/assets/985cb5ea-7f03-419b-9e01-f74851530982" />













