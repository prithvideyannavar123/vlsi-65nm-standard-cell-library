# Tri-State Inverter — UMC 65nm CMOS

Tri-state inverter designed in UMC 65nm, extending the basic inverter with enable-controlled High-Z output capability for shared-bus architectures.

---

## Topology

Two transistors are stacked in series in each drive path:

| Path | Transistors | Control |
|------|-------------|---------|
| Pull-up | PM1 (input) + PM0 (enable_bar `ne`) | Both must be ON |
| Pull-down | NM0 (input) + NM1 (enable `e`) | Both must be ON |

**When e = 1 (ne = 0):** PM0 ON, NM1 ON → circuit functions as standard inverter  
**When e = 0 (ne = 1):** PM0 OFF, NM1 OFF → both paths broken → **High-Z output**

---

## Transistor Sizing

Stacking two transistors doubles the effective drive resistance vs. the basic inverter. To maintain timing compatibility, all transistor widths are **doubled**:

| Parameter | Value |
|-----------|-------|
| PMOS W (each) | 1160 nm (2 × 560 nm) |
| NMOS W (each) | 700 nm (2 × 350 nm) |
| Total Transistors | 4 (2 PMOS + 2 NMOS) |
| Enable Signals | `e` (active HIGH), `ne` (active LOW) |

---

## Simulation Results

| Operating Mode | Output | Peak Current |
|----------------|--------|-------------|
| Enabled (e=1) | Inverts input, rail-to-rail | ~103.8 μA |
| Disabled (e=0) | High-Z (holds charge on load) | ~0 μA |

Peak current is **73% higher** than the basic inverter due to doubled transistor widths.  
Zero dynamic power consumption in the disabled state — critical for bus architectures.

| Verification | Status |
|---|---|
| DRC | CLEAN |
| LVS | CORRECT |

---

## Physical Layout

- **Cell height:** same as inverter (~2.0 μm) — compatible for placement in the same row
- **Cell width:** ~2× inverter (4 transistors to accommodate)
- **Internal routing:** Metal 1 wire connecting stacked transistors (adds small parasitic capacitance)
- **N-well:** full cell width, housing both PMOS transistors
- All diffusion regions carry duplicate contacts per UMC 65nm DRC rules

---

## Files

> Cadence Virtuoso schematic, layout, and simulation results are not included due to licence restrictions. All quantitative results are captured in this README and in the full report (`docs/EEE8127_Final_Report.pdf`).
