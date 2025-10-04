

# VSDBabySoC – Functional Modelling & FPGA Synthesis

This repository demonstrates the **functional modelling, simulation, and FPGA synthesis** of the **VSDBabySoC design**, a compact RISC-V-based System on Chip (SoC). The project integrates the **RVMYTH CPU core**, **PLL**, and **DAC**, and is designed for educational and verification purposes.

---

## Table of Contents

* [Introduction](#introduction)
* [Objective](#objective)
* [BabySoC Modules](#babysoc-modules)
* [Simulation Setup](#simulation-setup)
* [Simulation Steps](#simulation-steps)
* [Observations & Waveforms](#observations--waveforms)
* [FPGA Synthesis with Yosys](#fpga-synthesis-with-yosys)
* [Errors Encountered & Lessons Learned](#errors-encountered--lessons-learned)
* [Next Steps](#next-steps)

---

## Introduction

VSDBabySoC is a compact yet highly capable **SoC based on RISC-V architecture**, designed to:

* Integrate and test multiple open-source IP cores.
* Calibrate analog components such as DAC.
* Serve as a learning platform for **SoC fundamentals** and **RTL verification**.

**Key Modules:**

* **RVMYTH:** Small RISC-V CPU core.
* **PLL:** 8x phase-locked loop for stable clock generation.
* **DAC:** 10-bit digital-to-analog converter for analog interfacing.

**Functionality Overview:**

1. **Initialization & Clock Generation:** BabySoC activates the PLL to generate a stable clock for synchronization.
2. **Data Processing:** RVMYTH processes data, updating internal registers to feed the DAC.
3. **Analog Signal Generation:** DAC converts processed digital data to analog output for external devices.

---

## Objective

* Learn **SoC fundamentals** and functional verification.
* Perform **BabySoC simulation** using **Icarus Verilog & GTKWave**.
* Synthesize the SoC for FPGA using **Yosys** and understand **gate-level mapping**.

---

## BabySoC Modules

| Module/File                       | Description                                |
| --------------------------------- | ------------------------------------------ |
| `avsddac.v`                       | DAC module for analog output               |
| `avsdpll.v`                       | PLL for clock generation                   |
| `clk_gate.v`                      | Clock gating logic                         |
| `pseudo_rand.sv`                  | Pseudo-random signal generation            |
| `pseudo_rand_gen.sv`              | Testbench helper for pseudo-random signals |
| `rvmyth.tlv`                      | RISC-V CPU core (TL-Verilog)               |
| `testbench.v`                     | Main simulation testbench                  |
| `testbench.rvmyth.post-routing.v` | Post-routing testbench                     |
| `vsdbabysoc.v`                    | Top-level SoC integration module           |

---

## Simulation Setup

**Tools Required:**

* **Icarus Verilog (`iverilog`)** – Compile Verilog modules.
* **GTKWave** – View `.vcd` waveform files for signal analysis.

---

## Simulation Steps

1. **Clone the BabySoC repo:**

```bash
git clone https://github.com/manili/VSDBabySoC
cd VSDBabySoC/src/module/
```

2. **Compile Verilog modules:**

```bash
iverilog -o ../../output/babysoc_sim.out -I ../include *.v testbench.v
```

3. **Run simulation:**

```bash
./../../output/babysoc_sim.out
```

4. **Open waveform in GTKWave:**

```bash
gtkwave ../../output/babysoc_sim.vcd
```

**Signals to Observe:**

* **Reset:** All modules initialize properly.
* **Clock:** PLL provides a stable, periodic clock.
* **Dataflow:** RVMYTH outputs correctly update DAC inputs.

> **Screenshots:**
>
> * Terminal showing compilation and simulation.
> * GTKWave waveform of reset, clock, and dataflow signals.

---

## Observations & Waveforms

### Reset Operation

**Screenshot:** `screenshots/reset_waveform.png`
**Observation:** All registers are properly initialized.

### Clock Signal

**Screenshot:** `screenshots/clock_waveform.png`
**Observation:** Clock is stable, driving sequential elements correctly.

### Dataflow Between Modules

**Screenshot:** `screenshots/dataflow_waveform.png`
**Observation:** RVMYTH updates registers, DAC converts digital signals to analog-like outputs.

---

## FPGA Synthesis with Yosys

### Repository Structure

```
VSDBabySoC/
├── src/
│   ├── module/
│   │   ├── vsdbabysoc.v
│   │   ├── rvmyth.v
│   │   └── clk_gate.v
│   └── include/
│       └── sp_verilog.vh
├── lib/
│   ├── avsdpll.lib
│   ├── avsddac.lib
│   └── sky130_fd_sc_hd__tt_025C_1v80.lib
└── output/
    └── synth/
```

> **Screenshot:** `screenshots/repo_structure.png`

---

### Yosys Synthesis Workflow

```
# 1. Load top module
read_verilog ./src/module/vsdbabysoc.v

# 2. Load CPU core
read_verilog -I ./src/include ./src/module/rvmyth.v

# 3. Load clock gate
read_verilog -I ./src/include ./src/module/clk_gate.v
# (comment out include "sp_verilog.vh" in clk_gate.v if missing)

```
![img](https://github.com/DHANASRI-A/RISC-V-Chip-Tapeout---Week_2/blob/e8d11dc725b16cdc4a60a621daf8f55aec53966b/Pictures/Screenshot%202025-10-04%20160821.png)

```

# 4. Read standard cell / custom libraries
read_liberty -lib ./src/lib/avsdpll.lib
read_liberty -lib ./src/lib/avsddac.lib
read_liberty -lib ./src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# 5. Check hierarchy
hierarchy -check -top vsdbabysoc

# 6. Synthesis
synth -top vsdbabysoc

# 7. Map D flip-flops
dfflibmap -liberty ./src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# 8. Optimize
opt

# 9. Technology mapping using ABC
abc -liberty ./src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib -script +strash;scorr;ifraig;retime;{D};strash;dch,-f;map,-M,1,{D}

# 10. Flatten and clean
flatten
setundef -zero
clean -purge
rename -enumerate
stat

# 11. Write synthesized Verilog
write_verilog -noattr ./output/synth/vsdbabysoc.synth.v
```

> **Screenshots:**
>
> * `screenshots/synth_summary.png` – synthesis summary.
> * `screenshots/flattened_stat.png` – flattened netlist stats.
> * `screenshots/synth_file.png` – generated synthesized Verilog.

---

## Errors Encountered & Lessons Learned

1. Module paths incorrect → fixed by correcting `-I` and file paths.
2. Output folder missing → created manually before `write_verilog`.
3. Library loading errors → verified `.lib` files exist in `lib/`.
4. Yosys commands vs shell commands → `mkdir` must be done in terminal, not inside Yosys.

> **Screenshot:** `screenshots/error_messages.png` (optional)

---

## Next Steps

* Perform **FPGA place-and-route**.
* Analyze **timing and power** for the SoC.
* Extend design with additional IPs/peripherals.
* Combine simulation and gate-level verification for robust SoC development.

---
