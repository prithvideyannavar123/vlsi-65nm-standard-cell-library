# VLSI Standard Cell Library — UMC 65nm CMOS

> **EEE8127 Microelectronics Coursework · Newcastle University · February 2026**  
> Full-custom VLSI design, physical verification, and experimental characterisation using Cadence Virtuoso on UMC 65nm Low-Leakage CMOS technology.

---

## Overview

This project covers the complete ECAD flow for a standard cell library — from transistor-level schematic capture through physical layout, parasitic extraction (Calibre PEX), and analogue simulation — followed by three experimental studies on real circuit behaviour.

| Part | Scope |
|------|-------|
| **Part A** | Design & verify: Inverter, Tri-State Inverter, D Flip-Flop |
| **Part B** | Experiments: Fanout delay, Wire delay, Supply voltage scaling |

---

## Repository Structure

```
vlsi-65nm-standard-cell-library/
│
├── README.md
├── docs/
│   └── EEE8127_Final_Report.pdf        # Full technical report
│
├── part-a/                              # Standard Cell Library Design
│   ├── inverter/
│   │   └── README.md                   # Inverter design notes & results
│   ├── tri-state-inverter/
│   │   └── README.md                   # Tri-state inverter design notes & results
│   └── d-flip-flop/
│       └── README.md                   # D flip-flop design notes & results
│
└── part-b/                              # Experimental Characterisation
    ├── fanout-delay/
    │   └── README.md                   # FO2/FO4/FO6/FO8 results & Logical Effort analysis
    ├── wire-delay/
    │   └── README.md                   # Short / straight / zigzag wire comparison
    └── voltage-scaling/
        └── README.md                   # Delay & energy vs VDD sweep (0.6V–2.0V)
```

> **Note:** Cadence Virtuoso schematic/layout files and simulation data are not included due to licence restrictions. All key results, waveforms, and analysis are documented in each subfolder README and in the full report under `docs/`.

---

## Tools & Technology

| Item | Detail |
|------|--------|
| **Process** | UMC 65nm Low-Leakage (LL) CMOS |
| **ECAD Suite** | Cadence Virtuoso (Schematic, Layout, ADE XL) |
| **Simulator** | Cadence Spectre |
| **Physical Verification** | Calibre DRC / LVS / PEX (parasitic extraction) |
| **Supply Voltage** | 1.0 V (nominal) |

---

## Part A — Standard Cell Library

### Inverter
| Parameter | Value |
|-----------|-------|
| PMOS W/L | 560 nm / 60 nm |
| NMOS W/L | 350 nm / 60 nm |
| Width Ratio (Wp:Wn) | 1.6:1 |
| Post-layout tpHL | 1008 ps |
| Peak Supply Current | ~60 μA |
| DRC / LVS | CLEAN / CORRECT |

The 1.6:1 ratio (vs theoretical 2:1) arises from UMC 65nm DRC constraints requiring duplicate contacts on all source/drain diffusions — the minimum compliant NMOS width is 350 nm. Post-layout delay is ~20–30% higher than pre-layout, confirming the necessity of parasitic extraction.

### Tri-State Inverter
| Parameter | Value |
|-----------|-------|
| PMOS W (each) | 1160 nm (2× inverter) |
| NMOS W (each) | 700 nm (2× inverter) |
| Total Transistors | 4 (2P + 2N) |
| Output States | HIGH / LOW / High-Z |
| Peak Current (enabled) | ~103.8 μA |
| DRC / LVS | CLEAN / CORRECT |

Each transistor is doubled in width to compensate for the drive resistance penalty of stacked series devices, restoring timing compatibility with the basic inverter.

### D Flip-Flop
| Parameter | Schematic | Extracted | Overhead |
|-----------|-----------|-----------|----------|
| Dynamic Energy (0–10 ns) | 89.49 fJ | 174.6 fJ | **+95.1%** |
| Avg. Power (500 MHz equiv.) | ~44.7 μW | ~87.3 μW | +42.6 μW |
| LVS | — | CORRECT | 14T / 12nets / 6ports |

Layout parasitics nearly double the dynamic energy — a critical finding for power budgeting. Schematic-only simulation would underestimate real chip power by ~2×.

---

## Part B — Experimental Results

### Fanout Delay (FO2 – FO8)

Extracted input capacitance Cin = 1.72 fF (vs 0.96 fF schematic) — **1.79× increase** from layout parasitics.  
FO4 calibrated unit delay: τ_sch = 7.47 ps, τ_ext = 10.98 ps.

| Fanout | tp_avg Schematic (ps) | tp_avg Extracted (ps) | Extracted Overhead |
|--------|----------------------|----------------------|-------------------|
| FO2 | 26.54 | 38.48 | +44.97% |
| FO4 | 37.34 | 54.92 | +47.07% |
| FO6 | 67.69 | 90.21 | +33.27% |
| FO8 | 62.83 | 82.63 | +31.51% |

Logical Effort predictions match within 0–29% when τ is calibrated from FO4.

### Wire Delay

Schematic simulation is **completely blind** to wire delay — all three configurations give identical ~36.36 ps schematic delays.

| Wire Type | Extracted Delay (ps) | Wire Contribution (ps) | Equiv. Fan-out Loads |
|-----------|---------------------|----------------------|---------------------|
| Short (baseline) | 59.25 | — | — |
| Long Zigzag | 92.86 | 33.61 | ~7× Cload |
| Long Straight | 151.30 | 92.05 | ~19× Cload |

**Key insight:** The zigzag wire is **38.6% faster** than the straight wire at a similar total length — routing topology (perpendicular vs. parallel to supply rails) determines coupling capacitance as much as length does.

### Supply Voltage Scaling & Minimum Energy Point

50-stage inverter chain swept from VDD = 0.6 V to 2.0 V.  
Circuit fails to switch below 0.6 V (deep sub-threshold).

| VDD (V) | tp_avg (ns) | tp/stage (ps) | Energy (aJ) | vs MEP |
|---------|-------------|---------------|-------------|--------|
| 0.6 | 8.865 | 177.3 | 346.1 | +1091% |
| 0.8 | 2.208 | 44.2 | 52.11 | +79.5% |
| 1.0 | 1.131 | 22.6 | 79.96 | +175% |
| **1.2 ★** | **0.772** | **15.4** | **29.04** | **MEP** |
| 1.4 | 0.600 | 12.0 | 38.18 | +31.5% |
| 1.6 | 0.503 | 10.1 | 62.51 | +115% |
| 2.0 | 0.415 | 8.29 | 289.0 | +895% |

**Minimum Energy Point (MEP) at VDD = 1.2 V (29.04 aJ).** Below the MEP, both delay and energy improve simultaneously — no trade-off. Recommended operating range: **1.2 – 1.6 V**.

---

## Key Conclusions

1. **DRC drives sizing decisions** — manufacturing rules (duplicate contacts) pushed NMOS width from theoretical 280 nm to 350 nm, shifting Wp:Wn from 2:1 to 1.6:1. Real designs must navigate process rules, not only theory.

2. **Schematic simulation is insufficient** — layout parasitics added 20–30% to inverter delay, nearly doubled DFF energy (+95.1%), and 31–47% to all fanout delays. Post-layout extraction is mandatory for timing and power sign-off.

3. **Routing topology matters as much as wire length** — a zigzag Metal 1 route was 38.6% faster than a straight route of similar length, purely from reduced coupling to supply rails.

4. **The MEP is at 1.2 V** — below it there is no delay/energy trade-off (both improve). Above 1.6 V, energy rises faster than delay improves.

---

## Author

**Newcastle University — School of Engineering**  
Module: EEE8127 Microelectronics  
February 2026
