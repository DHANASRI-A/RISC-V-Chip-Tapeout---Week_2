# BabySoC Functional Modelling & Simulation

This repository demonstrates **functional modelling and simulation** of the VSDBabySoC project. The goal is to understand the **SoC fundamentals** and verify its **module interactions** using simulation tools like **Icarus Verilog** and **GTKWave**.

---

## Table of Contents

* [Introduction](#introduction)
* [Objective](#objective)
* [BabySoC Modules](#babysoc-modules)
* [Simulation Setup](#simulation-setup)
* [Simulation Steps](#simulation-steps)
* [Observations & Waveforms](#observations--waveforms)
* [Conclusion](#conclusion)

---

## Introduction

VSDBabySoC is a compact RISC-V-based **System on Chip (SoC)** designed to integrate and test multiple open-source IP cores:

* **RVMYTH:** A small RISC-V CPU core.
* **PLL:** 8x phase-locked loop for generating a stable clock.
* **DAC:** 10-bit digital-to-analog converter for interfacing with analog devices.

The purpose of this project is to **simulate the SoC behavior at the RTL level**, verify module interactions, and observe signal dataflow before performing any synthesis.

---

## Objective

* Gain practical understanding of **SoC components** and their interaction.
* Perform **functional simulation** of BabySoC.
* Analyze signals using **GTKWave** to verify:

  * Reset operation
  * Clocking
  * Dataflow between modules

---

## BabySoC Modules

The following Verilog files are used in this project:

| Module/File                       | Description                                |
| --------------------------------- | ------------------------------------------ |
| `avsddac.v`                       | DAC module for analog output               |
| `avsdpll.v`                       | PLL for stable clock generation            |
| `clk_gate.v`                      | Clock gating logic                         |
| `pseudo_rand.sv`                  | Pseudo-random signal generation            |
| `pseudo_rand_gen.sv`              | Testbench helper for pseudo-random signals |
| `rvmyth.tlv`                      | RISC-V CPU core (TL-Verilog)               |
| `testbench.v`                     | Main simulation testbench                  |
| `testbench.rvmyth.post-routing.v` | Post-routing simulation testbench          |
| `vsdbabysoc.v`                    | Top-level BabySoC integration module       |

---

## Simulation Setup

**Tools Used:**

* **Icarus Verilog (`iverilog`)** – Compile Verilog code and generate simulation executable.
* **GTKWave** – View waveform files (`.vcd`) for signal analysis.

**Prerequisite:** Ensure the BabySoC Verilog modules are available in your working directory.

---

## Simulation Steps

1. **Clone the BabySoC repository:**

```bash
git clone https://github.com/manili/VSDBabySoC
cd VSDBabySoC/src/module/
```

2. **Compile the Verilog modules using Icarus Verilog:**

```bash
iverilog -o ../../output/babysoc_sim.out -I ../include *.v testbench.v
```

> Explanation:
>
> * `-o` specifies the output executable.
> * `-I ../include` adds the header file directory.
> * `*.v` includes all Verilog modules.
> * `testbench.v` contains the testbench to drive simulation.

3. **Run the simulation to generate `.vcd` waveform files:**

```bash
./../../output/babysoc_sim.out
```

4. **Open `.vcd` file in GTKWave:**

```bash
gtkwave ../../output/babysoc_sim.vcd
```

> In GTKWave, observe the following signals:
>
> * **Reset** – Ensure all modules initialize correctly.
> * **Clock** – Verify PLL generates a stable periodic clock.
> * **Dataflow** – Observe how RVMYTH output goes to DAC (`OUT`), and how other signals change over time.

---

## Observations & Waveforms

### 1. Reset Operation

**Screenshot:** Include a screenshot of the reset signal waveform in GTKWave here.

**Observation:** The reset signal initializes all registers in BabySoC correctly and ensures the SoC starts in a known state.

---

### 2. Clock Signal

**Screenshot:** Include a screenshot showing the clock waveform from PLL.

**Observation:** Clock is stable and periodic. All sequential elements (flip-flops) respond correctly to the clock edges.

---

### 3. Dataflow Between Modules

**Screenshot:** Include a screenshot showing RVMYTH register output and DAC output.

**Observation:**

* RVMYTH updates its register values as per the instruction sequence.
* DAC correctly converts digital values to analog-like outputs (observed as digital in simulation).
* Signals are consistent with expected module interaction.

---

## Conclusion

* BabySoC simulation confirms proper **module integration**.
* Functional modelling helps identify potential issues **before synthesis**.
* GTKWave visualization provides a clear understanding of **signal timing and dataflow**.
* This forms a solid foundation for **RTL design verification and SoC development**.

---

### Screenshots

* **Reset waveform:** Place after Step 4 explanation.
* **Clock waveform:** Place after Step 4 explanation.
* **Dataflow signals:** Place under Observations section.

---


