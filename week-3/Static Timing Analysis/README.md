# ⚡STATIC TIMING ANALYSIS (STA)

## 🧠 What is STA?

Static Timing Analysis (STA) is a process of analyzing all possible timing paths in a circuit statically (i.e., without dynamic simulation).
It ensures that signals arrive on time at all flip-flops and output pins.
It determines:

- If the circuit meets setup and hold timing requirements.

- The maximum clock frequency the design can safely operate at.

- Potential timing violations (setup or hold).

## ⚙️ 2. Why STA is Needed

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

### Directed Acyclic Graph (DAG) / Timing Graph

Directed Acyclic Graph (DAG) is a fundamental structure used wherever order, dependency, and no cycles are essential —
from chip timing to software compilation and parallel computation.

In STA, a DAG helps trace timing paths, propagate delays, and ensure no feedback in combinational logic.

<img width="1366" height="736" alt="Screenshot (26)" src="https://github.com/user-attachments/assets/efd952db-9fb7-434c-af94-a82b8bab26f3" />




















