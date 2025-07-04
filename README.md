# Dynamic-Branch-Prediction

This simulator implements three dynamic branch prediction strategies: **Bimodal**, **Gshare**, and **Hybrid**. It processes MIPS instruction traces and evaluates prediction accuracy across varying configurations. Results are printed to standard output and can be visualized for performance analysis.
The predictors are tested against benchmark traces (`gcc`, `jpeg`, `perl`) to evaluate prediction accuracy and understand how various parameters affect branch misprediction rates.

---
## Project Description
Modern processors rely on **branch predictors** to guess the direction of conditional branches. Correct predictions reduce pipeline stalls and increase instruction throughput. This project explores:
- Table indexing using program counters (PCs)
- Global history usage and its impact
- Combining predictors via a meta-chooser
The simulator reads a **trace file** of branch instructions, runs predictions using selected predictor logic, and calculates **total predictions, mispredictions, misprediction rate**, and final state of predictor tables.

---

## Code Logic & Structure
The simulator is implemented in `sim_bp.cc`. Below is an overview of each predictor and its working mechanism:

---

### Bimodal Predictor

**Indexing**: Lower `m` bits of the PC  
**Storage**: A table of 2-bit saturating counters  
**Prediction**:  
- `counter < 2`: predict **not taken**
- `counter ≥ 2`: predict **taken**

**Update**: Increment/decrement counter based on actual outcome.

---

### Gshare Predictor

**Indexing**: XOR of global history register and upper PC bits  
**Global History**: `n`-bit shift register updated after every branch  
**Prediction & Update**: Same as bimodal, but index is generated using XOR logic

**Advantage**: Differentiates between dynamic instances of the same branch using history

---

### Hybrid Predictor

**Components**:  
- Bimodal predictor
- Gshare predictor
- Chooser predictor (indexed with `k` bits from PC)

**Mechanism**:  
- Both bimodal and gshare make predictions  
- Chooser selects which prediction to trust  
- Each component updates independently based on outcome  
- Chooser table adjusts when one predictor is correct and the other is not

**Goal**: Adaptively choose the better predictor at runtime.

### Execution Flow
1. Initialize predictors and counters
2. Parse trace line-by-line
3. Update prediction stats
4. Print results and final predictor states
5. Free memory


---

## Inputs

Each line in the trace file contains: <hex_address> <outcome>

Where:
- <hex_address> is the PC address of a branch instruction
- <outcome> is 't' (taken) or 'n' (not taken)

## Simulation of predictors

The simulator accepts command-line arguments as follows:

### Bimodal

```bash
./sim bimodal <M2> <trace_file>

parameters:
M2: Indexing bits for table (size = 2^M2)
trace_file: Text file containing branch traces
```
### Gshare
```bash
./sim gshare <M1> <N> <trace_file>

parameters:
M1: Table index bits
N: Global history register bits
trace_file: Branch trace file
```
### Hybrid
```bash
./sim hybrid <K> <M1> <N> <M2> <trace_file>

parameters:
K: Chooser index bits
M1: Gshare index bits
N: Global history bits
M2: Bimodal index bits
trace_file: Branch trace file
```
### OUTPUT
For each simulation, the program prints the number of predictions, number of mispredictions, and the misprediction rate, along with the final contents of the predictor tables. The [Output folder](https://github.com/Keta-Suthar/Dynamic-branch-predictor/tree/main/Output) contains: 
- The val_*.txt files generated during simulation
- A comparison file showing the performance of Bimodal and Gshare predictors across varying M and N values

For further details contact me on: ksuthar@ncsu.edu
