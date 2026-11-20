# 8-Bit Carry Select Adder (CSLA) – Full-Custom VLSI Design

A full-custom CMOS implementation of an **8-bit Carry Select Adder** built hierarchically from hand-designed standard cells (Full Adder, 2×1 MUX, D Flip-Flop, Inverter) in **Cadence Virtuoso**. Every cell has schematic, symbol, and layout views and is verified with **DRC** and **LVS**.

> VLSI Laboratory Project – Dept. of Electrical & Electronic Engineering, United International University (UIU)

---

## Overview

The Carry Select Adder speeds up addition by computing the sum and carry for both possible carry-in values (`Cin = 0` and `Cin = 1`) in parallel, then using multiplexers to pick the correct result once the real carry arrives. This removes most of the carry-propagation wait found in a ripple-carry adder.

In this project the 8-bit adder is split into 4-bit sections. The upper section computes both cases in parallel, and 2×1 multiplexers select the result based on the carry from the lower section. Registers (DFFs) and a clock tree are included at the inputs and outputs for synchronized operation.

## Design Specifications

| Item | Details |
|---|---|
| Design tool | Cadence Virtuoso |
| Technology | 45 nm CMOS |
| Architecture | 8-bit Carry Select Adder (2 × 4-bit sections) |
| Standard cells | Full Adder, 2×1 Multiplexer, D Flip-Flop, Inverter |
| Standard cell height | 1.8 µm |
| Routing layers | Poly, Metal1, Metal2, Metal3 |
| Simulation | Cadence Spectre (pre-layout transient simulation) |
| Verification | DRC and LVS (Cadence PVS) |

## Building Blocks

| Block | Description |
|---|---|
| **1-bit Full Adder** | Pass-transistor logic; SUM implements 3-input XOR, Cout implements the majority function. PMOS on top, NMOS on bottom, tap cell on the right. |
| **4-bit Full Adder** | Four 1-bit full adders cascaded (Cout → Cin). Layout built from two stacked rows, upper row flipped to share power rails (total height 3.6 µm). |
| **1-bit DFF** | Master–slave D flip-flop using transmission gates and inverters. CLK routed on Metal2. |
| **4-bit / 5-bit DFF** | Parallel 1-bit DFFs sharing a common clock. Upper rows flipped vertically; VDD tied on M1, VSS on M3. |
| **1-bit MUX (2×1)** | Pass-transistor multiplexer: `S = 0 → Y = A`, `S = 1 → Y = B`. |
| **4-bit MUX** | Four 1-bit MUXes with a common select signal; selects the correct 4-bit sum from the two parallel adder paths. |
| **Inverter** | CMOS inverter (PMOS W = 240 nm, NMOS W = 120 nm, L = 45 nm). Used for control signals and the clock tree. |

## Top-Level Architecture

The top-level `mid` schematic is built from the verified cell symbols:

- 3 × `4_bit_Full_Adder` (lower block, plus upper block computing for Cin = 0 and Cin = 1)
- 2 × `5bit_DFF` + 1 × `4bit_DFF` and further DFFs for input/output registering
- 1 × `4b_MUX2x1` to select the correct upper 4-bit sum
- 1 × `MUX21_usetrans` to select the final carry-out
- Inverter-based **clock tree** distributing CLK (CLK_A … CLK_F) to the register blocks

**Ports:** `A[7:0]`, `B[7:0]`, `Cin`, `CLK`, `S[7:0]`, `OUT`, `VDD`, `VSS`

## Results

| Metric | Result |
|---|---|
| Layout height | 14.4 µm |
| Layout width | 20.56 µm |
| Reported layout area | 310.4 µm² |
| Average power | ≈ 9.283 µW (`average(getData(":pwr" ?result "tran"))`) |
| Functional simulation | Sum and carry outputs verified for multiple input/carry-in combinations |

### Verification Status

| Cell | DRC | LVS |
|---|---|---|
| 1-bit Full Adder | Clean (0 errors) | Match |
| 4-bit Full Adder | Clean | Match |
| 1-bit DFF | Clean | Match |
| 4-bit DFF | Clean (0 errors) | Match |
| 5-bit DFF | Clean (0 errors) | Match |
| 1-bit MUX | Clean (0 errors) | Match |
| 4-bit MUX | Clean (0 errors) | Match |

DRC runs report 562 rule checks with 0 results; LVS runs report `ERC: Empty`, `Extraction: Clean`, `Comparison: Match`.

## Repository Structure (suggested)

```
.
├── schematics/        # Schematic views of all cells and the top-level CSLA
├── layouts/           # Layout views (GDS / Virtuoso cellviews)
├── simulation/        # Testbenches and Spectre results
├── verification/      # DRC / LVS reports
├── images/            # Schematics, layouts, waveforms used in the report
├── report/            # Project report (PDF)
└── README.md
```

## How to Reproduce

1. Open the library in **Cadence Virtuoso**.
2. Open the top-level `mid` cellview to inspect the hierarchical schematic.
3. Run a transient simulation in **ADE** (Spectre) using the provided testbench to check sum/carry outputs.
4. Run **DRC** and **LVS** (PVS) on each cell layout and the top-level layout.
5. Use the Visualization & Analysis calculator to measure average power.

## Future Work

- Further area optimization of the top-level layout and routing
- Post-layout (parasitic-extracted) simulation and delay/power comparison against pre-layout results
- Propagation-delay comparison with a ripple-carry adder baseline
- Extension to 16/32-bit CSLA

## Authors

| Name | ID |
|---|---|
| Readun Nabi Nafi | 021 223 0019 |
| Mohammad Amiruzzaman Jim | 021 223 0010 |
| Shahriyar Amin Shibbir | 021 223 0044 |
| Md. Samiul Islam | 021 223 0090 |

**Section:** A
**Supervisor:** Dr. Md. Hasanuzzaman, Associate Professor, Dept. of EEE, United International University
**Submission date:** 5 October 2026
