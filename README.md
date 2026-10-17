# PCB Engineering Notes

Working notes from a contract PCB design and assembly shop — [AimPCB](https://aimpcb.com) / Tianjin Huaxing Chengda Electronics.

Each entry is the outline of a full review we published, with the findings that mattered and the standards they came from. The complete write-ups, including schematics and layouts, are linked out to the site.

Boards covered here are ones we were asked to quote, review, fix or manufacture. Customer-identifying details are removed.

---

## Index

| Review | Topology / domain | Link |
|---|---|---|
| Flyback Power Supply PCB Design | 12 V / 1 A offline isolated AC-DC | [Full article](https://aimpcb.com/blog/flyback-power-supply-pcb-design/) |

---

## Flyback Power Supply PCB Design — 12 V / 1 A Offline Isolated Converter

**Full article:** https://aimpcb.com/blog/flyback-power-supply-pcb-design/

An engineering review of a real offline isolated flyback supply, from the manufacturing side rather than the datasheet side. The architecture is conventional and appropriate for a 12 W isolated supply; the release risk sits in CAD data integrity, insulation definition and safety-component qualification.

### Design under review

| Item | Value |
|---|---|
| Output | 12 V / 1 A (≈ 12 W) |
| Input | 90–264 VAC universal |
| Controller | DK112, integrated 700 V BJT switch |
| Transformer | EE19 5+2 |
| Secondary rectifier | SR3100, 3 A / 100 V Schottky |
| Feedback | TL431 (2.495 V reference) + PC817C optocoupler |
| Setpoint divider | 18 kΩ / 4.7 kΩ → ≈ 12.05 V |
| Output filter | 680 µF / 10 µH / 680 µF π filter |
| Board | 60.7 × 35.7 mm, 2 layers, ≈ 35 µm copper |

### Outline

**1. Scope and design boundaries**
What the files actually describe, board geometry, bill of materials, and the three functional paths — energy, feedback, and the primary leakage clamp.

**2. Circuit principles and key components**
- Operating principle and power margin — a controller listed at 12 W used in a 12 W design
- Input stage and primary clamp — bulk capacitors at ≈ 93 % of their 400 V rating at 264 VAC; clamp loop area and the cost of parasitic inductance
- Secondary rectification and filtering — reverse-voltage stress is output plus reflected input plus ringing, not the 12 V output
- Feedback setpoint and stability — the ≈ 1.93 kHz output LC sits inside the control loop; PC817C CTR spans roughly 200–400 % at a single bin
- Safety capacitors and transformer — a voltage rating is not an X or Y safety class; "EE19 5+2" names a core, not an insulation system
- Fuse and MOV coordination — I²t, inrush, surge level and failure order

**3. PCB layout and isolation**
- Net classes to declare: `MAINS_L/N`, `RECTIFIED_HV`, `PRIMARY_HOT_GND`, `SWITCH_NODE`, `SECONDARY_0V`, `12V_OUT`
- Primary-to-primary functional spacing versus primary-to-secondary safety spacing
- Placement driven by switching-current loops, not visual symmetry
- Primary high-di/dt loop: `CBULK+ → T1 primary → U1 switch → CBULK−`
- Secondary high-di/dt loop: `T1 secondary → D2 → C6 → secondary return`
- Kelvin-sensed feedback divider, returned to the quiet output node
- No star point across the barrier — separation is a safety property, not a grounding preference

**4. Thermal design and EMI**
Loss distribution from actual pulsed waveforms rather than I × VF estimates; worst case at both line extremes; common-mode versus differential-mode suppression; why increasing the Y capacitor is not a free EMC improvement.

**5. Simulation and measurement approach**
SPICE across startup, line variation, load transients and short circuit; Monte Carlo over the TL431 / PC817 loop; FRA measurement of the real transfer function. Internal design targets used: ≥ 45° phase margin, 6 dB gain margin, ±3 % regulation, transients within ±5 %, ripple under ≈ 120 mV p-p.

**6. Risk priorities and corrective-action order**
Seven P0 release blockers, five P1 verification items, two P2 lifetime items, and the order in which they should be resolved.

### P0 findings

| Finding | Why it blocks release |
|---|---|
| Empty net stream, tracks without net assignment | Net-aware DRC cannot be trusted; rebuild from the released schematic and review the ECO |
| PCB and schematic designator mismatch | Unintentional differences must be dispositioned before release |
| ≈ 10 mil (0.254 mm) global clearance rule | Cannot be used to claim mains-to-SELV reinforced insulation |
| CY1 labelled only "222 / 400 V" | Crosses the isolation barrier; requires an approved Y-class part |
| CX1 labelled only "0.1 µF / 275 V" | Ordinary film is not an approved X2 capacitor |
| T1 specified only as "EE19 5+2" | No gap, ratio, winding construction, insulation system or hipot requirement |
| Ambiguous `GND` / `SGND` labels | A future ECO could logically join both domains |

### Standards referenced

- **IEC 60664-1** — insulation coordination: voltage, impulse conditions, pollution degree, material group, altitude
- **IEC 62368-1** — end-product safeguards for the energy sources involved
- **IEC 60384-14** — fixed capacitors for EMI suppression and connection to supply mains
- **IEC 61000-4-4** — electrical fast transient / burst immunity
- **IEC 61000-4-5** — surge immunity test methods, generators and levels
- **IPC-2221** — generic printed-board design

### Disclaimer

This review is based on historical design files and is for engineering reference only. It does not represent a certified, production-ready product. Any isolated power supply must undergo complete safety qualification against the applicable end-product standards for its target market — including insulation coordination, dielectric-strength testing and EMC compliance — before mass production.

---

## About

Written by the AimPCB engineering team — PCB design, design review and DFM, SMT / THT assembly, and turnkey PCBA.

- Site: https://aimpcb.com
- PCB design: https://aimpcb.com/pcb-design/
- Design review & DFM: https://aimpcb.com/pcb-design-review-dfm/
- Turnkey PCB assembly: https://aimpcb.com/turnkey-pcb-assembly/
- Contact: https://aimpcb.com/contact-us/
