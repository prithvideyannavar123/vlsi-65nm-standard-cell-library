# Wire Delay Experiment — UMC 65nm

Comparison of Metal 1 interconnect delay across three routing topologies: short (baseline), long straight, and long zigzag. All three share the same schematic; only the physical layout of the connecting wire changes.

---

## Experimental Setup

Two inverters (inv_1) connected in series.  
Delay measured from the intermediate node (net3, after inverter 1) to the output (Q, after inverter 2) at the 50% threshold using Cadence delay() calculator.

---

## Results

| Wire Type | Schematic Delay (ps) | Extracted Delay (ps) | Wire-Only Contribution (ps) | Ceq (Elmore) | Equiv. Fan-out Loads |
|-----------|---------------------|---------------------|---------------------------|--------------|---------------------|
| Short (baseline) | 36.36 | 59.25 | — | ≈ 0 fF | — |
| Long Zigzag | 36.36 | 92.86 | 33.61 | ≈ 6.96 fF | **~7× Cload** |
| Long Straight | 36.36 | 151.30 | 92.05 | ≈ 19.06 fF | **~19× Cload** |

Wire-only contribution = extracted delay − short-wire baseline (59.25 ps)  
Ceq from Elmore model: Ceq = ΔDelay / (0.69 × Rout), with Rout ≈ 7 kΩ

---

## Key Finding: Routing Topology vs. Wire Length

> The zigzag wire is **38.6% faster** than the straight wire despite comparable total Metal 1 length.

The straight wire runs **parallel** to VDD and GND rails for its full length — maximising broadside coupling capacitance to these supply conductors.  
The zigzag wire runs **perpendicular** to supply rails — minimising the coupling area and therefore the RC time constant.

This demonstrates that **routing orientation is as critical as wire length** for interconnect delay at 65nm.

---

## Short Wire — Baseline

- Schematic delay: 36.36 ps — intrinsic gate delay of second inverter, wire-length independent
- Extracted delay: 59.25 ps
- The 22.89 ps overhead is from **cell layout parasitics** (junction caps, Metal 1, contact resistance, well boundaries) — not wire RC
- This floor is unavoidable in any physical implementation and must be included in all timing budgets

---

## Long Straight Wire

- Extracted delay: 151.3 ps — **4.16× schematic baseline**
- Wire contribution: 92.05 ps above short-wire floor
- Ceq ≈ 19.06 fF — equivalent to connecting 19 additional fan-out loads at the driver output
- Significant edge rounding visible in waveforms due to RC low-pass filtering

---

## Long Zigzag Wire

- Extracted delay: 92.86 ps
- Wire contribution: 33.61 ps — **2.74× lower** than straight wire
- Ceq ≈ 6.96 fF — ~7× fan-out equivalent
- Reduced edge rounding compared to straight wire confirms lower effective RC loading

---

## Important Note

**Schematic simulation is completely blind to wire delay.**  
All three routing topologies produce identical schematic delays (~36.36 ps).  
Post-layout extraction is the only way to capture interconnect effects — and a few hundred micrometres of Metal 1 can double inverter delay at 65nm.

---

## Files

> Cadence Virtuoso schematic, layout, and simulation results are not included due to licence restrictions. All quantitative results are captured in this README and in the full report (`docs/EEE8127_Final_Report.pdf`).
