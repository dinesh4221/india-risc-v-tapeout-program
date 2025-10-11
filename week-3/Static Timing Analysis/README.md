# ⚡STATIC TIMING ANALYSIS (STA)

## 🧠 What is STA?

Static Timing Analysis (STA) is a process of analyzing all possible timing paths in a circuit statically (i.e., without dynamic simulation).
It ensures that signals arrive on time at all flip-flops and output pins.
It determines:

- If the circuit meets setup and hold timing requirements.

- The maximum clock frequency the design can safely operate at.

- Potential timing violations (setup or hold).

## ⚙️ Why STA is Needed

- Simulation can miss critical timing violations because it only checks specific input vectors.

- STA analyzes every possible path mathematically.

- It’s much faster and more exhaustive than timing simulation.

## 🧩 Key Timing Terms
| Term |	Meaning |
|------|----------|
| Clock period |	Time between two rising/falling edges of the clock |
| Setup time |	Minimum time before the clock edge that data must be stable |
| Hold time |	Minimum time after the clock edge that data must remain stable |
| Clock | Uncertainty	Variation in clock due to jitter or skew |
| Clock skew |	Difference in clock arrival times between two flip-flops |
| Slack |	Difference between required time and arrival time |
| Slew | Time it takes for a signal to change from one logic level to another |
| Data Arrival Time (DAT) |	When the signal actually arrives at the capturing flip-flop |
| Data Required Time (DRT) | When the signal is required to arrive to meet timing |

## Setup Analysis

Setup Analysis checks whether data arrives in time to be correctly captured by the next clock edge at a flip-flop or latch.

### When is Setup Time Important?

- Every flip-flop (FF) has a setup time — the minimum time before the active clock edge when the data input (D) must be stable 
to ensure reliable capture.If data changes too late, the flip-flop may capture the wrong value or metastability may occur.

- Setup Slack= (Data Required Time)−(Data Arrival Time)

- The circuit should satisfy:

  **Arrival Time ≤ Required Time**,

  which expands to **Clock-to-Q Delay + Combinational Delay + Skew ≤ Clock Period - Setup Time - Uncertainity**

## Hold Analysis

- Hold time is the minimum time after the active clock edge that the data input (D) of a flip-flop must remain stable.

- If data changes too soon after the clock edge, the flip-flop might capture the wrong data → Hold violation.

### Hold Analysis in STA

STA performs hold analysis to ensure that data launched by one clock edge does not reach the capture FF too early —
before the hold window closes.

- Hold Slack=(Actual Arrival Time)−(Required Arrival Time)

- Launch Flop Clock-to-Q Delay + Combinational Delay + Skew ≥ Capture Flop Hold Time + Hold Uncertainity

## Types of Setup/Hold Analysis

- Register → Register
- Input → Register
- Register → Output
- Input → Output
- Clock Gating
- Recovery/Removal
- Data to Data
- Latch

<details>

<summary>Directed Acyclic Graph (DAG) / Timing Graph</Summary>

Directed Acyclic Graph (DAG) is a fundamental structure used wherever order, dependency, and no cycles are essential —
from chip timing to software compilation and parallel computation.

In STA, a DAG helps trace timing paths, propagate delays, and ensure no feedback in combinational logic.

<img width="1366" height="736" alt="Screenshot (26)" src="https://github.com/user-attachments/assets/efd952db-9fb7-434c-af94-a82b8bab26f3" />

</details>

<details>
 <summary>Timing Reports</summary>

### What is a Timing Report?

A timing report in STA tools (like Synopsys PrimeTime, Cadence Tempus, or OpenSTA) summarizes timing information for a specific path or set of paths between two sequential elements (usually flip-flops).

It helps verify:

- Setup check (max delay)
- Hold check (min delay)
- Slack (timing margin)

### Typical Timing Report Sections
- Path Header
- Clock Information
- Data Path (Launch → Capture)
- Capture Clock Path
- Setup/Hold Requirement

### Example

 <div align="center">

<img width="558" height="517" alt="image" src="https://github.com/user-attachments/assets/56098262-1b81-4845-81d1-507f3fa03d67" />

 </div>

</details>

<details>
 <summary> On Chip Variations (OCV)</summary>
 
### 🧠 What is On-Chip Variation (OCV)?

Even though a chip is manufactured as a single die, not all transistors or interconnects behave identically.
Due to process variations, voltage drops, and temperature gradients, the delay of gates and wires can vary across different parts of the same chip.

These intra-die variations are called On-Chip Variations (OCV).

### ⚙️ Why OCV Matters in STA

In traditional STA, we assume one PVT corner (e.g., SS or FF) applies to the whole chip.
But in reality:

- Some cells may behave like SS (slow) corner.

- Others may behave like FF (fast) corner.

- If we don’t account for this variation, we may get over-optimistic timing results → possible silicon failures.

- So STA tools model OCV to make analysis more realistic and conservative.

### Etching Process Variations

Etching defines feature dimensions (like gate length, interconnect width, and dielectric thickness).
However, etching is not perfectly uniform across the wafer or die.

#### How It Causes OCV:

- Line-width variation:

   - Over-etch → narrower metal or polysilicon line → higher resistance → slower signal.
   - Under-etch → wider line → lower resistance → faster signal.

- Etch depth variation:

   - Varies interconnect cross-section → affects RC delay.
   - Example: uneven via or trench etch depth changes R of via and C between metals.

- Microloading / pattern dependency:

   - Dense areas etch slower → slightly thicker oxide or narrower lines.
   - Sparse areas etch faster → thinner oxide or wider lines.
   - Leads to within-die delay differences → modeled as spatial OCV.
 
<div align="center">

<img width="587" height="390" alt="image" src="https://github.com/user-attachments/assets/7d283658-0067-4a5a-b02b-96ea05a00e7a" />

</div>

### Oxide Thickness

<div align="center">

<img width="797" height="444" alt="image" src="https://github.com/user-attachments/assets/1bb64f52-c4ee-401d-bfb3-0b3a8eca78f2" />


</div>

### 📏  How OCV is Applied

OCV is modeled by derating the timing delays of launch and capture paths differently.

- For Setup Analysis:

   - We want to ensure data doesn’t arrive too late.So, assume worst case:

     - Launch path → slower (data delayed)
     - Capture path → faster (clock arrives early)

   - Setup check with OCV:
  
     - Data path delay × (1 + derate)
     - Clock path delay × (1 – derate)

- For Hold Analysis:

  - We want to ensure data doesn’t arrive too early.So, assume worst case:

    - Launch path → faster (data comes early)

    - Capture path → slower (clock arrives late)

  - Hold check with OCV:

     - Data path delay × (1 – derate)
     - Clock path delay × (1 + derate)

</details>


<details>
 <summary>OpenSTA</summary>

 ## ⚙️ What is OpenSTA?

OpenSTA (Open Source Static Timing Analyzer) is an open-source tool used to perform Static Timing Analysis (STA) on digital designs — mainly after synthesis and placement & routing (PnR).

It helps ensure that your circuit meets timing requirements like setup, hold, clock skew, and propagation delay without running simulation.

## 🔍 Why Use OpenSTA?

After synthesis or layout, your design has:

- Gate-level netlist (from Yosys)

- Timing libraries (.lib) from Sky130 PDK

- Constraints (.sdc) defining clock, input/output delays

👉 OpenSTA checks timing between flip-flops, combinational logic, and I/O ports to ensure:

- The circuit runs at the target frequency.

- No timing violations (setup/hold) exist.
  

</details>

























