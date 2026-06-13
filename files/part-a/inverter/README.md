# Inverter — UMC 65nm CMOS

Standard CMOS inverter designed and verified in UMC 65nm Low-Leakage technology using Cadence Virtuoso.

---

## Transistor Sizing

| Parameter | Value | Rationale |
|-----------|-------|-----------|
| PMOS W/L | 560 nm / 60 nm | 1.6× NMOS to compensate hole mobility |
| NMOS W/L | 350 nm / 60 nm | Minimum DRC-compliant width (dual contacts) |
| Width Ratio | 1.6:1 | DRC-driven; theoretical is 2:1 |
| Supply Voltage | 1.0 V | UMC 65nm nominal |

The classical 2:1 PMOS:NMOS ratio is derived from the ~2.5× electron-to-hole mobility difference. However, UMC 65nm mandates **duplicate contacts** on all source/drain diffusions. The minimum compliant diffusion width is:

```
2×65 nm (contact) + 70 nm (contact-to-contact spacing) + 2×60 nm (enclosure) = 320 nm
→ 350 nm with process margin
```

This shifts the ratio from 2:1 to 1.6:1, producing a rise/fall asymmetry below 20% — acceptable for digital standard cell use.

---

## Simulation Results

### Pre-Layout vs Post-Layout

| Metric | Pre-Layout | Post-Layout | Overhead |
|--------|-----------|-------------|----------|
| tpHL | ~800 ps | **1008 ps** | ~+26% |
| Peak Supply Current | — | ~60 μA | — |
| DRC | — | CLEAN | — |
| LVS | — | CORRECT | — |

Post-layout delay includes parasitic capacitances from Metal 1 routing, junction diffusions, polysilicon-to-metal overlaps, and contact resistances extracted by Calibre PEX.

### Waveform Observations
- Output correctly inverts input between 0 V and 1.0 V (rail-to-rail)
- Sharp current spikes (~60 μA) during transitions = crowbar current (brief simultaneous PMOS/NMOS conduction)
- Near-zero static current between transitions — fundamental CMOS low-power property

---

## Physical Layout

- **Cell height:** ~2.0 μm (standard cell discipline — placeable in rows)
- **Cell width:** ~1.0–1.2 μm
- **Power rails:** VDD at top, GND at bottom (horizontal Metal 1)
- **N-well:** spans full cell width for seamless adjacent-cell abutment
- **Contacts:** 2 per diffusion region (DRC requirement)
- **Gate:** polysilicon running vertically across active region
- **Output routing:** Metal 1 horizontal to cell edge

---

## Files

> Cadence Virtuoso schematic, layout, and simulation results are not included due to licence restrictions. All quantitative results are captured in this README and in the full report (`docs/EEE8127_Final_Report.pdf`).
