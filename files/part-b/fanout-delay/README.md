# Fanout Delay Experiment — UMC 65nm

Characterisation of inverter propagation delay as a function of gate fanout (FO2–FO8), with Logical Effort model validation across schematic and post-layout extracted views.

---

## Experimental Setup

One driving inverter (inv_1) drives N identical load inverters in parallel.  
Four configurations tested: **FO2, FO4, FO6, FO8**.  
Each simulated in both **schematic** (ideal) and **extracted** (av_extracted, Calibre PEX) views.

---

## Input Capacitance (Cin)

Measured by charge integration: Cin = Q / ΔV

| View | Integrated Charge Q | Cin |
|------|---------------------|-----|
| Schematic | 959.5 × 10⁻¹⁸ C | **0.96 fF** |
| Extracted | 1.723 × 10⁻¹⁵ C | **1.72 fF** |

Layout parasitics increase Cin by **1.79×** — this directly scales the unit delay τ used in all Logical Effort predictions.

---

## Propagation Delay Results

| Fanout | tpHL Sch (ps) | tpLH Sch (ps) | tp_avg Sch (ps) | tpHL Ext (ps) | tpLH Ext (ps) | tp_avg Ext (ps) | Extracted Overhead |
|--------|--------------|--------------|----------------|--------------|--------------|----------------|-------------------|
| FO2 | 22.81 | 30.27 | 26.54 | 44.84 | 32.11 | 38.48 | +44.97% |
| FO4 | 31.33 | 43.35 | 37.34 | 44.88 | 64.95 | 54.92 | +47.07% |
| FO6 | 75.43 | 59.94 | 67.69 | 77.41 | 103.00 | 90.21 | +33.27% |
| FO8 | 62.83 | 62.83 | 62.83 | 66.11 | 99.14 | 82.63 | +31.51% |

---

## Logical Effort Analysis

FO4 calibrated unit delay: **τ_sch = 7.468 ps**, **τ_ext = 10.983 ps**  
(τ_ext is 1.471× larger than τ_sch, consistent with the 1.79× Cin increase since τ ∝ R_eff × Cin)

Prediction formula: **tp = (N + 1) × τ**

| Fanout | LE Pred Sch (ps) | LE Pred Ext (ps) | LE Error |
|--------|-----------------|-----------------|----------|
| FO2 | 22.40 | 32.95 | ~0% (FO2) |
| FO4 | 37.34 | 54.92 | **0%** (calibration point) |
| FO6 | 52.28 | 76.88 | ~29% error |
| FO8 | 67.21 | 98.85 | ~7% error |

LE error at FO6 arises from PMOS/NMOS asymmetry — at FO6 the tpHL/tpLH difference is largest, causing the average to deviate from the perfectly linear LE assumption.

---

## Key Observations

**FO2:** tpHL (22.81 ps) < tpLH (30.27 ps) in schematic — NMOS pull-down drives faster at light loads. Post-extraction reverses this for tpHL specifically due to differential parasitic loading paths.

**FO4:** Industry-standard benchmark. Both delays increase vs FO2 confirming linear load scaling. Extracted tpLH (64.95 ps) >> tpHL (44.88 ps) — layout parasitics preferentially load the structurally weaker PMOS pull-up path.

**FO6:** Highest schematic average delay of all configurations. tpHL dominates in schematic; post-extraction tpLH dominates — consistent pattern of parasitics amplifying the PMOS/NMOS asymmetry.

**FO8:** Most symmetric schematic result (tpHL = tpLH = 62.83 ps) — at heavy load both transistors are capacitance-dominated and drive-strength differences matter less. Asymmetry reappears post-extraction.

---

## Files

> Cadence Virtuoso schematic, layout, and simulation results are not included due to licence restrictions. All quantitative results are captured in this README and in the full report (`docs/EEE8127_Final_Report.pdf`).
