# POST SYNTHESIS

## 🧩 What is Post-Synthesis Simulation?

**Post-synthesis simulation (PSS)** is the verification step performed after synthesis — i.e., after your RTL design (in Verilog) has been converted into a gate-level netlist consisting of standard cells (like `sky130_fd_sc_hd__and2_1`, `inv_1`, etc.).

It checks:

- Whether the synthesized (gate-level) circuit still behaves functionally identical to the RTL design.

- Whether timing delays introduced by gates and interconnections affect the circuit’s correct operation.

## ⚙️ Flow of Post-Synthesis Simulation
| Step |	Description |
|------|--------------|
| 1. RTL Design |	Write behavioral Verilog |
| 2. Synthesis |	Use a synthesis tool (like Yosys) to map the RTL to standard cells from a PDK |
| 3. Functional Verification |	Run simulation without timing delays (using -DFUNCTIONAL) to ensure the logic is still correct |
| 4. Timing Verification (Gate-Level Simulation) |	Include cell delays (-DUNIT_DELAY=#1 or from .lib timing files) to check that setup/hold times and propagation delays don’t cause glitches |
| 5. Comparison |	Compare gate-level waveforms to RTL simulation waveforms — they should match (except for small timing shifts) |

## 🔍 Files Used

| File                        | Description                                             |
| --------------------------- | ------------------------------------------------------- |
| `vsdbabysoc.synth.v`        | Synthesized gate-level netlist from Yosys               |
| `sky130_fd_sc_hd.v`         | Standard cell Verilog models (defines logic of gates)   |
| `testbench.v`               | Same testbench used for RTL simulation                  |
| `.lib` or `.sdf` (optional) | Contains timing information for delay-based simulations |

## Netlist Generation (Using YOSYS)

### Standard Library Cells
```bash
read_liberty -lib /home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/src/lib/avsdpll.lib        # PLL cell library
read_liberty -lib /home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/src/lib/avsddac.lib        # DAC cell library
read_liberty -lib /home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  # Sky130 standard cell library
```
### Read verilog RTL files

```bash
# babysoc module
read_verilog /home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/src/module/vsdbabysoc.v

# rvmyth module
read_verilog -I /home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/src/include /home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/src/module/rvmyth.v

# Clock gating module
read_verilog -I /home/idinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/src/include /home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/src/module/clk_gate.v

```

### Synthesis and Technology Mapping

```bash
synth -top vsdbabysoc
dfflibmap -liberty /home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
opt
abc -liberty /home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
flatten
setundef -zero
clean -purge
rename -enumerate
```

<img width="554" height="620" alt="rvmyth components" src="https://github.com/user-attachments/assets/67e4900f-5e21-466a-8454-4032ca566f99" />


<img width="554" height="355" alt="vsd components" src="https://github.com/user-attachments/assets/aabf6aea-b720-488d-8267-580b9514be0a" />

#### Design Heirarchy

<img width="499" height="710" alt="design heirarchy" src="https://github.com/user-attachments/assets/411f8e62-d654-4374-a810-cf6a95e814d3" />


### Netlist 

```bash
write_verilog -noattr /home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/output/post_synth_sim/vsdbabysoc.synth.v
```

## Simulation (Using `Iverilog` and `GTKWave`)

```bash
iverilog -o /home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/output/post_synth_sim/post_synth_sim.out
-DPOST_SYNTH_SIM -DFUNCTIONAL -DUNIT_DELAY=#1 -I
/home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/output/src/include -I
/home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/src/module -I
/home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/output/post_synth_sim/vdbabysoc.synth.v -I
/home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/src/lib
/home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/src/module/testbench.v

cd /home/dinesh-kumar-reddy-guntaka/Desktop/VSDBabySoC/output/post_synth_sim

./post_synth_sim.out

gtkwave post_synth_sim.vcd

```

## 📈 Waveform Analysis




















































