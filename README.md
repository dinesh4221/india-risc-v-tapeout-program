jj

# Week-0(Task-1 Introduction to the Program)

## 📘 Digital VLSI Planning in SoC Design

### Overview

Digital VLSI planning in System-on-Chip (SoC) design is the process of integrating processors, memory, interconnects, and peripherals on a single chip while meeting performance, power, and area goals.

### Program Objective

We will learn to design a SoC from RTL(Registe transfer level coding) to GDSII(Graphic Data System II) using open source tools in this program. 

SoC design:
RTL coding➡️Simulation➡️Synthesis➡️Netlist➡️Physical Design➡️Layout➡️GDSII

### 🔑 Key Steps

- Specification – Define system functionality, performance, power, and area constraints.

- Architecture Design – Partition system into modules (IP cores, accelerators, buses/NoCs).

- RTL Design & Verification – Implement and validate logic before physical design.

* Physical Design Planning

  * Floorplanning & placement

  * Clock tree synthesis

  * Routing & timing closure

- Low-Power Techniques – Clock gating, power gating, multi-voltage domains.
- DFT (Design for Testability) – Ensure chip can be tested after faication.

- Sign-off & Tape-out – Final checks for timing, power, and manufacturability before fabrication.

### 🎯 Outcome

* Efficient SoC design that achieves:

  * High performance

  * Low power consumption

  * Optimized silicon area

  * Cost-effectiveness
  
<img width="1097" height="569" alt="Image" src="https://github.com/user-attachments/assets/6c5b89f2-d8c7-4940-9d6c-568e7ee41a31" />




# Week-0 (TASK-2 Tools Installation)

### **System Requirements**
- 6 GB RAM
- 50 GB HDD
- Ubuntu 20.04 or higher
- 4 vCPU

| Tool | Purpose | 
|------|---------|
| **Yosys** | RTL Synthesis & Logic Optimization |
| **Iverilog** | Compiler & Simulator for Verilog |
| **GTKWave** | Waveform Analysis and Visualize signals over time|

### **TOOLs**

#### <ins>**Yosys Installation**</ins>
```bash
$ sudo apt-get update
$ git clone https://github.com/YosysHQ/yosys.git
$ cd yosys
$ sudo apt install make               # we have to install make if not installed
$ sudo apt-get install build-essential clang bison flex \
    libreadline-dev gawk tcl-dev libffi-dev git \
    graphviz xdot pkg-config python3 libboost-system-dev \
    libboost-python-dev libboost-filesystem-dev zlib1g-dev
$ make config-gcc
# You need to run the following command before running make because Yosys build depends on a Git submodule called abc, which hasn't been initialized yet.
$ make 
$ sudo make install
```
<img width="806" height="261" alt="Image" src="https://github.com/user-attachments/assets/decf1167-b3c0-4974-8c2c-49a6321e7841" />

#### <ins>**Iverilog Installation**</ins>
```bash
$ sudo apt-get update
$ sudo apt-get install iverilog
```
<img width="887" height="621" alt="Image" src="https://github.com/user-attachments/assets/f685cdd0-6f77-4c97-a70d-e593c790d2ae" />

#### <ins>**gtkwave**</ins>
```bash
$ sudo apt-get update
$ sudo apt install gtkwave
```

<img width="903" height="112" alt="Image" src="https://github.com/user-attachments/assets/4d616228-f2a0-492d-8abf-1b4cf38147c9" />


