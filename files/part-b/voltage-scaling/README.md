# Supply Voltage Scaling & Minimum Energy Point — UMC 65nm

Parametric VDD sweep (0.6 V – 2.0 V) on a 50-stage CMOS inverter chain, characterising propagation delay and energy per switching event to identify the Minimum Energy Point (MEP).

---

## Experimental Setup

- 50-stage CMOS inverter chain in Cadence Spectre
- VDD swept from 0.6 V to 2.0 V (step: 0.2 V)
- Below 0.6 V: no valid logic transitions (deep sub-threshold — transistors fail to switch)
- Delay measured at 50% threshold via H-markers in waveform viewer

---

## Propagation Delay vs. VDD

Three distinct operating regions:

| Region | VDD Range | Behaviour |
|--------|-----------|-----------|
| Near-threshold | 0.6 – 0.8 V | High sensitivity: 0.2 V increase → **4× delay reduction** (8.86 ns → 2.21 ns) |
| Transition | 0.8 – 1.2 V | Continued improvement: **2.9× reduction** (2.21 ns → 0.77 ns) — most efficient region |
| Saturation | 1.2 – 2.0 V | Diminishing returns: only **1.86× improvement** across 0.8 V more supply |

Full dataset:

| VDD (V) | tp_avg (ns) | tp/stage (ps) |
|---------|-------------|---------------|
| 0.6 | 8.865 | 177.3 |
| 0.8 | 2.208 | 44.2 |
| 1.0 | 1.131 | 22.6 |
| **1.2** | **0.772** | **15.4** |
| 1.4 | 0.600 | 12.0 |
| 1.6 | 0.503 | 10.1 |
| 1.8 | 0.444 | 8.87 |
| 2.0 | 0.415 | 8.29 |

---

## Rise/Fall Asymmetry

- At 0.6–1.0 V: tpLH/tpHL ≈ 1.0 — both transistors equally limited near threshold
- Above 1.2 V: asymmetry develops — ratio drops to **0.74 at 2.0 V** (tpLH 26% shorter than tpHL)
- Cause: at high overdrive the PMOS benefits disproportionately from each additional unit of VDD vs. the structurally under-sized NMOS (1.6:1 ratio design)

---

## Energy vs. VDD — Minimum Energy Point

Energy per switching event = VDD × ∫I dt over the propagation delay window.

**MEP at VDD = 1.2 V → 29.04 aJ**

| VDD (V) | Charge ×10⁻¹⁸ C | Energy (aJ) | vs MEP |
|---------|----------------|-------------|--------|
| 0.6 | 576.8 | 346.1 | +1091% |
| 0.8 | 65.14 | 52.11 | +79.5% |
| 1.0 | 79.96 | 79.96 | +175% |
| **1.2 ★** | **24.20** | **29.04** | **MEP** |
| 1.4 | 27.27 | 38.18 | +31.5% |
| 1.6 | 39.07 | 62.51 | +115% |
| 1.8 | 69.18 | 124.5 | +329% |
| 2.0 | 144.5 | 289.0 | +895% |

### Why the U-shaped profile?

**Left limb (0.6–1.2 V):** As VDD increases, switching time shortens so dramatically that ∫I dt falls faster than the VDD multiplier rises → net energy decreases.  
At 0.6 V the crowbar period is so extended that charge = 576.8 × 10⁻¹⁸ C, nearly **24× the MEP value**.

**Anomaly at 1.0 V (79.96 aJ > 0.8 V):** At this specific voltage both transistors are simultaneously in saturation for a maximum fraction of the switching cycle, creating a local short-circuit current peak unique to this process node.

**Right limb (1.2–2.0 V):** Switching is fast but the **quadratic VDD² term** now dominates: energy rises from 29.04 aJ to 289.0 aJ (+895%) for only 1.67× voltage increase.

---

## Design Recommendations

| Operating Region | VDD | Trade-off |
|-----------------|-----|-----------|
| Below MEP | < 1.2 V | Both delay and energy worsen — avoid |
| **MEP (optimal)** | **1.2 V** | **Best energy, reasonable delay** |
| Classic trade-off | 1.2–1.6 V | 35% delay reduction at 2.15× energy cost — reasonable for most designs |
| Diminishing returns | > 1.6 V | 18% more delay reduction at 4.6× energy cost — only for latency-critical applications |

**Recommended operating range: 1.2 – 1.6 V**

---

## Files

> Cadence Virtuoso schematic, layout, and simulation results are not included due to licence restrictions. All quantitative results are captured in this README and in the full report (`docs/EEE8127_Final_Report.pdf`).
