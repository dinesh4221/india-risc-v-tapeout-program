
# Week-1 (TASK-2 Tools Installation)

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

### **TOOL CHECK**

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


