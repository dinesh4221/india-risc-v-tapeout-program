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

<div align="center">
 
<img width="554" height="620" alt="rvmyth components" src="https://github.com/user-attachments/assets/67e4900f-5e21-466a-8454-4032ca566f99" />

</div>

<div align="center">
 
<img width="554" height="355" alt="vsd components" src="https://github.com/user-attachments/assets/aabf6aea-b720-488d-8267-580b9514be0a" />

</div>

#### Design Heirarchy

<div align="center">
 
<img width="499" height="710" alt="design heirarchy" src="https://github.com/user-attachments/assets/411f8e62-d654-4374-a810-cf6a95e814d3" />

</div>

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

<div align="center">

<img width="838" height="450" alt="waveform comparison" src="https://github.com/user-attachments/assets/c3643ecc-7f9e-40ca-832d-f68aed170249" />

</div>

**Gate-Level Simulation** ensures your synthesized design works exactly like your RTL and meets all timing requirements before tapeout or fabrication.


# Timing Analysis of VSDBabySoC

## OpenSTA Installation

```bash
git clone https://github.com/parallaxsw/OpenSTA.git
cd OpenSTA
```

### Install Docker

```bash
sudo apt  install podman-docker
```

### Build Docker Image

```bash
docker build --file Dockerfile.ubuntu22.04 --tag opensta .
docker run -i -v $HOME:/data opensta
```

- OpenSTA is ready to use.

### Prepare Required Files

```bash
/data/VSDBabySoC/OpenSTA$ mkdir -p examples/timing_libs/
/data/VSDBabySoC/OpenSTA/examples$ ls timing_libs/
avsddac.lib  avsdpll.lib  sky130_fd_sc_hd__tt_025C_1v80.lib

/data/VSDBabySoC/OpenSTA$ mkdir -p examples/BabySoC
/data/VSDBabySoC/OpenSTA/examples$ ls BabySoC/
gcd_sky130hd.sdc  vsdbabysoc_synthesis.sdc  vsdbabysoc.synth.v
```

### Run Min/Max timing delays

```bash
read_liberty -min /data/VSDBabySoC/OpenSTA/examples/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib
read_liberty -max /data/VSDBabySoC/OpenSTA/examples/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib

read_liberty -min /data/VSDBabySoC/OpenSTA/examples/timing_libs/avsdpll.lib
read_liberty -max /data/VSDBabySoC/OpenSTA/examples/timing_libs/avsdpll.lib

read_liberty -min /data/VSDBabySoC/OpenSTA/examples/timing_libs/avsddac.lib
read_liberty -max /data/VSDBabySoC/OpenSTA/examples/timing_libs/avsddac.lib

read_verilog /data/VSDBabySoC/OpenSTA/examples/BabySOC/vsdbabysoc.synth.v
link_design vsdbabysoc

read_sdc /data/VSDBabySoC/OpenSTA/examples/BabySOC/vsdbabysoc_synthesis.sdc

report_checks
```

- `link_desgn` sets `vsdbabysoc` as the top module.

- Execute inside docker using the command:

```bash

docker run -it -v $HOME:/data opensta /home/Desktop/VSDBabySoC/OpenSTA/examples/BabySoC/vsdbabysoc_min_max_delays.tcl

```

### PVT Corner Analysis

PVT stands for:

- P → Process

- V → Voltage

- T → Temperature

In real silicon, transistor behavior varies depending on manufacturing variations, supply voltage, and temperature.

So, to ensure your VSDBabySoC works under all conditions, you must check timing at multiple PVT corners.

### Automated STA across all PVT Corners

```bash
 # List of standard cell liberty files
set list_of_lib_files(1) "sky130_fd_sc_hd__tt_025C_1v80.lib"
set list_of_lib_files(2) "sky130_fd_sc_hd__ff_100C_1v65.lib"
set list_of_lib_files(3) "sky130_fd_sc_hd__ff_100C_1v95.lib"
set list_of_lib_files(4) "sky130_fd_sc_hd__ff_n40C_1v56.lib"
set list_of_lib_files(5) "sky130_fd_sc_hd__ff_n40C_1v65.lib"
set list_of_lib_files(6) "sky130_fd_sc_hd__ff_n40C_1v76.lib"
set list_of_lib_files(7) "sky130_fd_sc_hd__ss_100C_1v40.lib"
set list_of_lib_files(8) "sky130_fd_sc_hd__ss_100C_1v60.lib"
set list_of_lib_files(9) "sky130_fd_sc_hd__ss_n40C_1v28.lib"
set list_of_lib_files(10) "sky130_fd_sc_hd__ss_n40C_1v35.lib"
set list_of_lib_files(11) "sky130_fd_sc_hd__ss_n40C_1v40.lib"
set list_of_lib_files(12) "sky130_fd_sc_hd__ss_n40C_1v44.lib"
set list_of_lib_files(13) "sky130_fd_sc_hd__ss_n40C_1v76.lib"

# Read custom libs first
read_liberty /data/VSDBabySoC/OpenSTA/examples/timing_libs/avsdpll.lib
read_liberty /data/VSDBabySoC/OpenSTA/examples/timing_libs/avsddac.lib

# Loop over standard cell libraries
for {set i 1} {$i <= [array size list_of_lib_files]} {incr i} {

    read_liberty /data/VSDBabySoC/OpenSTA/examples/timing_libs/$list_of_lib_files($i)
    read_verilog /data/VSDBabySoC/OpenSTA/examples/BabySOC/vsdbabysoc.synth.v
    link_design vsdbabysoc
    current_design
    read_sdc /data/VSDBabySoC/OpenSTA/examples/BabySOC/vsdbabysoc_synthesis.sdc

    check_setup -verbose

    report_checks -path_delay min_max -fields {nets cap slew input_pins fanout} -digits {4} \
        > /data/VSDBabySoC/OpenSTA/examples/BabySOC/STA_OUPUT/min_max_$list_of_lib_files($i).txt

    exec echo "$list_of_lib_files($i)" >> /data/VSDBabySoC/OpenSTA/examples/BabySOC/STA_OUPUT/sta_worst_max_slack.txt
    report_worst_slack -max -digits {4} >> /data/VSDBabySoC/OpenSTA/examples/BabySOC/STA_OUPUT/sta_worst_max_slack.txt

    exec echo "$list_of_lib_files($i)" >> /data/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUPUT/sta_worst_min_slack.txt
    report_worst_slack -min -digits {4} >> /data/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUPUT/sta_worst_min_slack.txt

    exec echo "$list_of_lib_files($i)" >> /data/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUPUT/sta_tns.txt
    report_tns -digits {4} >> /data/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUPUT/sta_tns.txt

    exec echo "$list_of_lib_files($i)" >> /data/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUPUT/sta_wns.txt
    report_wns -digits {4} >> /data/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUPUT/sta_wns.txt
}
```

```bash

docker run -it -v $HOME:/data opensta /data/VSDBabySoC/OpenSTA/examples/BabySoC/sta_across_pvt.tcl

```

### Ouput Directory

```bash

/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT$ ls
min_max_sky130_fd_sc_hd__ff_100C_1v65.lib.txt
min_max_sky130_fd_sc_hd__ss_100C_1v40.lib.txt
sta_worst_max_slack.txt
sta_worst_min_slack.txt
sta_tns.txt
sta_wns.txt

```

## Timing Summary

### Worst Hold Slack

<div align="center">
  
<img width="727" height="370" alt="worst hold slack" src="https://github.com/user-attachments/assets/ec5c6f95-cb4b-4b3a-ab73-a4124aa90aa0" />

</div>

### Worst Setup Slack

<div align="center">
  
<img width="727" height="370" alt="worst setup slack" src="https://github.com/user-attachments/assets/df2d0668-c11f-4232-b4f7-5d912ea06509" />

</div>

### Worst Negative Slack

<div align="center">
  
<img width="727" height="370" alt="worst negative slack" src="https://github.com/user-attachments/assets/39e18ec5-bace-4df0-8d54-9bf1897712af" />

</div>

### Total Negative Slack

<div align="center">

<img width="727" height="367" alt="Total negative slack" src="https://github.com/user-attachments/assets/1c08e871-a996-413b-ae31-111195090789" />

</div>




























































