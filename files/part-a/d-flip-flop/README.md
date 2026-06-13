# D Flip-Flop — UMC 65nm CMOS

Full-custom master-slave D flip-flop in UMC 65nm, verified with DRC/LVS and characterised for post-layout energy overhead.

---

## Behaviour

- Q captures the value of D **only at the rising edge of CLK**
- Q holds its value unchanged between clock edges, regardless of D
- nQ is the precise complement of Q at all times
- Near-zero static power between transitions — CMOS low-power property

---

## Implementation

| Parameter | Value |
|-----------|-------|
| Topology | Master-slave (two edge-triggered latches) |
| Total Transistors | 14 |
| Internal Nets | 12 |
| Ports | 6 (D, CLK, Q, nQ, VDD, GND) |
| Supply Voltage | 1.0 V |

---

## Energy Analysis — Schematic vs. Extracted

Energy calculated via: **E = VDD × ∫I dt** over 0–10 ns simulation window.  
With VDD = 1.0 V: charge in fC = energy in fJ numerically.

| Parameter | Schematic | Extracted | Difference |
|-----------|-----------|-----------|------------|
| Total charge (0–10 ns) | 89.49 fC | 174.6 fC | +85.11 fC |
| Dynamic energy | 89.49 fJ | 174.6 fJ | **+95.1%** |
| Avg. power (500 MHz) | ~44.7 μW | ~87.3 μW | +42.6 μW |

**The near-doubling of energy is caused entirely by layout parasitics** — junction capacitances, Metal 1 routing wires, polysilicon-to-metal overlaps, and via structures that do not appear in the schematic but all require charge from VDD every time they switch.

> A designer who budgets from schematic simulation alone would underestimate real chip power by approximately **50%** — a critical error in any battery-constrained or thermally-limited design.

---

## Physical Verification

| Check | Result | Notes |
|-------|--------|-------|
| DRC | 3 violations | PLY.D, L2.D1, PLY_F.S2.RCM.Pri — all **global density rules**, not cell geometry errors. Resolved by automated dummy fill at chip level. |
| LVS | **CORRECT** | 14 transistors, 12 nets, 6 ports — exact match between layout and schematic |

LVS CORRECT is the most critical verification — it confirms that the physical layout faithfully implements the intended circuit and all simulation results are representative of actual silicon.

---

## Physical Layout

- **Cell height:** standard cell height — compatible with inverter row placement
- **Transistor arrangement:** 14 transistors in master-slave topology
- **PMOS:** upper rows, connected to VDD rail at top
- **NMOS:** lower rows, connected to GND rail at bottom
- **Internal routing:** dense Metal 1 implementing master and slave latch feedback paths
- **Clock distribution:** upper metal layer to minimise RC delay to both latch stages

---

## Testbench Configuration

- V1: CLK pulse source (0→1 V, period 2 ns)
- V2: D pulse source (different period to test both capture and hold)
- C0: 1 fF load capacitor at Q (models downstream gate capacitance)
- V0: VDD = 1.0 V
- Both schematic and av_extracted (Calibre PEX) views simulated

---

## Files

> Cadence Virtuoso schematic, layout, and simulation results are not included due to licence restrictions. All quantitative results are captured in this README and in the full report (`docs/EEE8127_Final_Report.pdf`).
