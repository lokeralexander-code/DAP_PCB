# Buck-Boost (Intermediate Rail) — Working Document

**Project:** Digital Audio Player
**Rev:** 0 (working)
**Status:** In progress — not reviewed

**Author:** Alexander Loker — design decisions, component selection, analysis and verification.
**Drafted by:** Claude (Anthropic) — document structure and prose, written from the author's direction, source data and datasheet extracts.

---

## 1. Part Identification

| Field | Value |
|---|---|
| AL-PN | |
| Manufacturer | Analog Devices (Linear Technology) |
| MPN | LTC3440 — orderable suffix TBD |
| Description | |
| Distributor | |
| Distributor PN | |
| Package | 10-lead plastic MSOP (MS) |
| Footprint | |
| Mount type | SMD |
| Component type | IC — Buck-boost converter |
| Library status | |
| Project | DAP |
| Lead time | |
| Datasheet rev | |
| Unit price | |
| Symbol library | ./aloker-Schematics.SchLib |
| Footprint library | ./aloker-Footprints.PcbLib |

---

## 2. Design Requirements

### 2.1 Inputs

| Requirement | Value | Source |
|---|---|---|
| Input rail | SYS (BQ24250 output) | Power tree |
| Input voltage range | 3.0 – 4.2 V | Cell range |
| Output voltage | 3.9 V | Intermediate rail target |
| Switching frequency | 1.2 MHz | Author's choice |
| Load | Analog chain only, via LT3042 (+3.3 V) and charge pump → LT3093 (−3.3 V). LCD, SD, MCU excluded. | Author |
| Output current, peak | ~180 mA (S4: 580 mW across ±3.3 V ≈ 88 mA per rail; charge pump draws ≈ its output current from 3.9 V) | Power Budget Rev 3, §4.4 |
| Output current, design | 250 mA | Peak + margin (author) |

### 2.2 Constraints

- **Low noise** — the rail feeds the LT3042 / LT3093 analog LDOs.
- **Small footprint.**

---

## 3. Pin Configuration

| Pin | Name | Connection | Notes |
|---|---|---|---|
| 1 | RT | R_T to GND | Sets oscillator frequency |
| 2 | MODE/SYNC | GND (logic low) | Low = fixed frequency, high = Burst Mode (datasheet p.1, p.9) |
| 3 | SW1 | Inductor | |
| 4 | SW2 | Inductor | |
| 5 | GND | GND | |
| 6 | VOUT | 3.9 V rail; top of FB divider | |
| 7 | VIN | SYS | |
| 8 | SHDN/SS | | |
| 9 | FB | FB divider midpoint | 1.22 V reference |
| 10 | VC | | Error amp output |

---

## 4. External Components

| Ref | Function | Value | Tolerance | Rating | MPN | Notes |
|---|---|---|---|---|---|---|
| R_T | Frequency set | 50 kΩ | 0.1% | 1/10 W, 0603 thin film | YAGEO RT0603BRD0750KL | #1-075, DigiKey 13-RT0603BRD0750KLCT-ND, 20 wk |
| R1 | FB divider, VOUT → FB | 220 kΩ | 0.1% | 1/16 W, 0402 thick film | YAGEO RC0402BR-07220KL | #1-074, DigiKey 13-RC0402BR-07220KLCT-ND, 17 wk |
| R2 | FB divider, FB → GND | 100 kΩ | 0.1% | 1/10 W, 0603 thin film | YAGEO RT0603BRD07100KL | #1-066, DigiKey YAG1235CT-ND, 20 wk |
| L | Power inductor | 6.8 µH | ±20% | I_sat 2.3 / 3.5 / 4.6 A (10/20/30% drop); I_rms 3.1 / 4.0 A (20/40 °C rise); DCR 74.1 mΩ max; SRF 26 MHz; 4.1 × 4.1 × 2.8 mm | Bourns SRP4030FA-6R8M | #1-076, DigiKey SRP4030FA-6R8MCT-ND, 22 wk. AEC-Q200. Inductance specified at 100 kHz |
| C_IN | Input capacitor | | | | | |
| C_OUT | Output capacitor | | | | | |

---

## 5. Calculations

### 5.1 Switching frequency

**Equation:**

$$f = \frac{6\times10^{10}}{R_T}\ \text{Hz}$$

**Inputs:** $f = 1.2\ \text{MHz}$

**Result:**

$$R_T = \frac{6\times10^{10}}{1.2\times10^{6}} = 50\ \text{k}\Omega$$

**Datasheet reference:** Operation — Oscillator

---

### 5.2 Output voltage divider

**Equation:**

$$V_{OUT} = 1.22\ \text{V}\left(1 + \frac{R_1}{R_2}\right)$$

**Inputs:** $V_{OUT} = 3.9\ \text{V}$

**Result:**

$$\frac{R_1}{R_2} = \frac{3.9}{1.22} - 1 = 2.1967\ \text{(target)}$$

Selected $R_1 = 220\ \text{k}\Omega$, $R_2 = 100\ \text{k}\Omega$:

$$V_{OUT} = 1.22 \times \left(1 + \frac{220}{100}\right) = 3.904\ \text{V (nominal)}$$

Over the feedback-voltage range $1.196$–$1.244\ \text{V}$ (datasheet p.2): $V_{OUT} = 3.827$–$3.981\ \text{V}$.

Divider current: $I_{div} = 3.904\ \text{V} / 320\ \text{k}\Omega \approx 12.2\ \mu\text{A}$.

**Datasheet reference:** Pin Functions — FB (Pin 9)

---

### 5.3 Minimum inductance

**Equation** ($f$ in MHz, $\text{Ripple}$ = inductor current ripple as a fraction of $I_{OUT(MAX)}$):

$$L_{boost} > \frac{V_{IN(MIN)}\left(V_{OUT} - V_{IN(MIN)}\right)}{f \cdot I_{OUT(MAX)} \cdot \text{Ripple} \cdot V_{OUT}}\ \mu\text{H}$$

$$L_{buck} > \frac{V_{OUT}\left(V_{IN(MAX)} - V_{OUT}\right)}{f \cdot I_{OUT(MAX)} \cdot \text{Ripple} \cdot V_{IN(MAX)}}\ \mu\text{H}$$

**Inputs:** $V_{IN(MIN)} = 3.0\ \text{V}$, $V_{IN(MAX)} = 4.2\ \text{V}$, $V_{OUT} = 3.9\ \text{V}$, $f = 1.2\ \text{MHz}$, $I_{OUT(MAX)} = 0.25\ \text{A}$, $L = 6.8\ \mu\text{H}$ (selected)

**Result:**

$$\text{Ripple}_{boost} = \frac{3.0 \times 0.9}{1.2 \times 0.25 \times 6.8 \times 3.9} = 0.34 \;\Rightarrow\; \Delta I_L = 85\ \text{mA}_{pp}$$

$$\text{Ripple}_{buck} = \frac{3.9 \times 0.3}{1.2 \times 0.25 \times 6.8 \times 4.2} = 0.14 \;\Rightarrow\; \Delta I_L = 34\ \text{mA}_{pp}$$

Peak inductor current (boost, 3.0 V in, $\eta \approx 0.85$):

$$I_{L,avg} = \frac{0.25 \times 3.9}{3.0 \times 0.85} = 0.38\ \text{A}, \qquad I_{L,pk} = 0.38 + \frac{0.085}{2} \approx 0.42\ \text{A}$$

**Datasheet reference:** Applications Information — Inductor Selection

---

### 5.4 Output capacitance

**Equation** ($C_{OUT}$ in F, $f$ in Hz; %Ripple = output *voltage* ripple as a percentage of $V_{OUT}$):

$$\%\text{Ripple}_{boost} = \frac{I_{OUT(MAX)}\left(V_{OUT} - V_{IN(MIN)}\right) \cdot 100}{C_{OUT} \cdot V_{OUT}^{2} \cdot f}\ \%$$

$$\%\text{Ripple}_{buck} = \frac{I_{OUT(MAX)}\left(V_{IN(MAX)} - V_{OUT}\right) \cdot 100}{C_{OUT} \cdot V_{IN(MAX)} \cdot V_{OUT} \cdot f}\ \%$$

Transient rule of thumb (datasheet p.12): multiply the charge-ripple $C_{OUT}$ by $f / f_{crossover}$.

**Inputs:** $I_{OUT(MAX)} = 0.25\ \text{A}$, $V_{IN(MIN)} = 3.0\ \text{V}$, $V_{IN(MAX)} = 4.2\ \text{V}$, $V_{OUT} = 3.9\ \text{V}$, $f = 1.2\ \text{MHz}$, $\%\text{Ripple}_V = 0.5\%$ ($\Delta V_{OUT} = 19.5\ \text{mV}_{pp}$)

**Result** (minimum effective capacitance for charge ripple):

$$C_{OUT,boost} = \frac{0.25 \times 0.9 \times 100}{0.5 \times 3.9^{2} \times 1.2\times10^{6}} = 2.47\ \mu\text{F}$$

$$C_{OUT,buck} = \frac{0.25 \times 0.3 \times 100}{0.5 \times 4.2 \times 3.9 \times 1.2\times10^{6}} = 0.76\ \mu\text{F}$$

Boost governs: $C_{OUT} \geq 2.47\ \mu\text{F}$ effective, after DC-bias derating at $3.9\ \text{V}$. Transient requirement pending loop crossover.

**Datasheet reference:** Applications Information — Output Capacitor Selection

---

## 6. Configuration (pin-strapped)

| Pin | Setting | Effect | Reason |
|---|---|---|---|
| MODE/SYNC | Tied low — fixed-frequency PWM (Burst Mode not used) | Constant 1.2 MHz switching at all loads | Low-noise constraint |
| SHDN/SS | | | |

### 6.1 Start-up behaviour

### 6.2 Shutdown behaviour

---

## 7. Thermal

| Parameter | Value | Source |
|---|---|---|
| T_JMAX | 125 °C | Datasheet, Pin Configuration |
| θ_JA, 1-layer board | 130 °C/W | Datasheet, Pin Configuration |
| θ_JA, 4-layer board | 100 °C/W | Datasheet, Pin Configuration |
| θ_JC | 45 °C/W | Datasheet, Pin Configuration |

**Working:**

---

## 8. Protection and Fault Behaviour

| Fault | Detection | Device response | System response |
|---|---|---|---|
| Forward overcurrent | Supply current limit on PMOS switch A: 2.7 A typ peak (p.8, p.15); ~2.5–2.9 A over −55 to 125 °C (p.5, Current Limit graph) | Switch A turned off; B and D on for remainder of cycle; foldback in sustained overload (p.15) | |
| Average input current | Guaranteed ≥ 1 A min (p.3, Input Current Limit) | | |
| Reverse current | −400 mA typ through switch D (p.8) | Switch D turned off | |
| | | | |
| | | | |

---

## 9. Layout Notes

---

## 10. Bring-up and Verification

| # | Check | Method | Pass criteria | Result |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |

---

## 11. Open Items

| # | Item | Blocking? | Owner | Resolved |
|---|---|---|---|---|
| 1 | FB divider resistor assignment | — | Alex | Yes — R1 = 220 k (#1-074), R2 = 100 k (#1-066) |
| 2 | Output current basis | — | Alex | Yes — analog only, 250 mA design |
| 3 | Inductor selection | — | Alex | Yes — SRP4030FA-6R8M (#1-076) |
| 4 | Inductor I_sat basis | — | Alex | Yes — rated to switch current limit: 10–20% L drop at 2.7–2.9 A |

---

## 12. Revision Log

| Rev | Date | Change |
|---|---|---|
| 0 | 2026-09-21 | Created from charger IC template; load basis set to analog only (250 mA design); MODE/SYNC fixed-frequency; inductor equations logged; current limits logged; inductor selected (SRP4030FA-6R8M); FB divider set R1 = 220 k / R2 = 100 k |
