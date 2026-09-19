# Charger IC — Working Document

**Project:** Digital Audio Player
**Rev:** 0
**Status:** Issued for review

**Author:** Alexander Loker — design decisions, component selection, analysis and verification.
**Drafted by:** Claude (Anthropic) — document structure and prose, written from the author's direction,
source data and datasheet extracts.

---

## 1. Part Identification

| Field | Value |
|---|---|
| AL-PN | #1-054 |
| Manufacturer | Texas Instruments |
| MPN | BQ24250RGET |
| Description | IC BATT CHG LI-ION 1CELL 24VQFN |
| Distributor | DigiKey |
| Distributor PN | 296-36180-1-ND |
| Distributor description | Charger IC Lithium Ion 24-VQFN (4x4) |
| Package | 24-VQFN (4x4) |
| Footprint | QFN50P400X400X100-25N |
| Mount type | SMD |
| Component type | IC — Charger |
| Supply type | Standard |
| Library status | Verified |
| Project | DAP |
| Lead time | 16 weeks |
| Datasheet rev | |
| Unit price | |
| Symbol library | ./aloker-Schematics.SchLib |
| Footprint library | ./aloker-Footprints.PcbLib |

---

## 2. Design Requirements

### 2.1 Inputs

| Requirement | Value | Source |
|---|---|---|
| Battery voltage | 3.0 – 4.2 V | Battery documentation |
| Battery amperage | 1.85 Ah | Battery documentation |
| Battery charge amperage (max) | 1900 mAh | Battery documentation |
| Standard charge current | 500 mA (0.27C) | Selected |
| Low output noise | DAP principle | N/A |
| | | |
| | | |

### 2.2 Constraints

---

## 3. Pin Configuration

| Pin | Name | Connection | Notes |
|---|---|---|---|
| 1 | CE | | |
| 2 | EN1 | | |
| 3 | EN2 | | |
| 4 | AGND | | |
| 5 | SDA | | |
| 6 | SCL | | |
| 7 | STAT | | |
| 8 | INT | | |
| 9 | TS | | |
| 10 | ISET | | |
| 11 | BAT | | |
| 12 | BAT-1 | | |
| 13 | SYS | | |
| 14 | SYS-1 | | |
| 15 | PGND | | |
| 16 | PGND-1 | | |
| 17 | SW | | |
| 18 | SW-1 | | |
| 19 | IN | | |
| 20 | PMID | | |
| 21 | BOOT | | |
| 22 | ILIM | | |
| 23 | VDPM | | |
| 24 | LDO | | |
| 25 | THERMALPAD | | |

---

## 4. External Components

| AL-PN | Ref | Function | Value | Package | Manufacturer | MPN | Distributor PN | Lead time | Notes |
|---|---|---|---|---|---|---|---|---|---|
| #1-063 | L | Inductor, SW to SYS | 1.0 µH ±20%, 2 A Isat / 2 A Irms, 96 mΩ max, shielded thin film | 0505 (1412 metric) | TDK | TFM141206BLE-1R0MTCA | 445-TFM141206BLE-1R0MTCACT-ND | 20 wk | Footprint TFM141206BLE. Replaces Bourns SRN2009T-1R0M (#1-057), which had insufficient saturation margin |
| #1-058 | — | CBAT / CLDO / CPMID | 1 µF, 16 V, X6S, ±10% | 0402 | Murata | GRM155C81C105KE11D | 490-12700-1-ND | 17 wk | Three positions |
| #1-059 | — | CBOOT | 33 nF, 16 V, X7R, ±10% | 0402 | Murata | GRM155R71C333KA01D | 490-3257-1-ND | 17 wk | Datasheet requires >15 V rating |
| #1-064 | RISET | Fast charge current set | 500 Ω, 0.1%, 1/10 W, thin film | 0603 | YAGEO | RT0603BRC07500RL | 13-RT0603BRC07500RLCT-ND | 20 wk | Sets I_FC = 500 mA |
| #1-065 | RILIM | Input current limit set | 270 Ω, 1%, 1/10 W, thick film | 0603 | YAGEO | RC0603FR-07270RL | 311-270HRCT-ND | 17 wk | Sets input limit = 1 A |
| #1-060 | R2 | TS network resistor 1 | 5 kΩ, 1%, 1/16 W | 0402 | YAGEO | RC0402FR-075KL | 13-RC0402FR-075KLCT-ND | 17 wk | Per datasheet worked example |
| #1-061 | R3 | TS network resistor 2 | 9.88 kΩ, 0.5%, 1/16 W | 0402 | YAGEO | RT0402DRD079K88L | 311-2214-1-ND | 20 wk | Nearest available to the 9.82 kΩ example |
| #1-062 | J | Battery connector | 3-position terminal block header, 5.08 mm, right angle | Through hole | Phoenix Contact | 1786417 | 277-5985-ND | 6 wk | BAT+, BAT−, NTC |
| #1-068 | F1 | Input filter — series element | 120 Ω @ 100 MHz, 3 A, 30 mΩ | 0805 (2012 metric) | Würth Elektronik | 742792023 | 732-1613-1-ND | **33 wk** | WE-CBF, 1 signal line. Footprint WE-CBF_0805_W4.0. Lossy above ~30 MHz; ~3.6 Ω at the 3 MHz fundamental. Longest lead time in the BOM |
| #1-071 | C1 | Input filter — bulk, connector side | 4.7 µF, 16 V, X7R, ±10% | 0805 (2012 metric) | Murata | GRM21BR71C475KE51L | 490-14466-1-ND | 17 wk | Footprint CAPC2012X140N |
| #1-071 | C2 | Input filter — damping branch capacitor | 4.7 µF, 16 V, X7R, ±10% | 0805 (2012 metric) | Murata | GRM21BR71C475KE51L | 490-14466-1-ND | 17 wk | Same part as C1. In series with R1 |
| #1-072 | R1 | Input filter — damping branch resistor | 0.5 Ω, 1%, 1/16 W, current sense thick film | 0402 (1005 metric) | YAGEO | RL0402FR-070R5L | 311-.5PCT-ND | 20 wk | Value from LTspice (Section 5.7). Pulse rating during hot plug to be confirmed against the manufacturer's pulse withstanding curve |
| #1-070 | C3 | CIN at charger IN pin | 2.2 µF, **25 V**, X7R, ±10% | 0603 (1608 metric) | Murata | GRM188Z71E225KE43D | 490-GRM188Z71E225KE43DCT-ND | 17 wk | Raised from 16 V to 25 V to reduce DC bias derating against the >2 µF requirement |
| #1-069 | J1 | USB-C receptacle | USB 2.0, 24 (16+8 dummy) position, 48 V, 5 A, right angle | Through hole | GCT | USB4085-GF-A | 2073-USB4085-GF-ACT-ND | 7 wk | Footprint GCT_USB4085-GF-A_REVA4. CC1/CC2 each need their own 5.1 kΩ pull-down; D+/D− appear on both A and B sides and must be tied |
| #1-049 | D1 | TVS diode, VBUS transient protection | 7 V standoff, 7.78 V breakdown, 12 V clamp, 33.3 A Ipp, unidirectional | SMA (DIOM5226X230N) | Diodes Incorporated | SMAJ7.0A-13-F | SMAJ7.0A-FDICT-ND | 12 wk | Place at the connector, ahead of the filter. Existing library part (aRTy project) |
| #1-073 | CSYS1, CSYS2 | Bulk capacitance at SYS | 22 µF, 25 V, X7S, ±10% | 1210 (3225 metric) | Murata | GCM32EC71E226KE36L | 490-GCM32EC71E226KE36LCT-ND | 21 wk | Qty 2. Footprint CAPC3225X270N |
| #1-048 | CSYS3 | High-frequency bypass at SYS | 0.1 µF, 50 V, X7R, ±10% | 0402 (1005 metric) | Murata | GRM155R71H104KE14D | 490-10700-1-ND | 17 wk | Low ESL, place closest to the pin. Existing library part (aRTy project) |
| | R_DPM1 | VDPM divider, upper | 274 kΩ, 1%, generic | 0402 | | | | | Sets VIN_DPM = 4.488 V with R_DPM2 |
| | R_DPM2 | VDPM divider, lower | 100 kΩ, 1%, generic | 0402 | | | | | |
| | R_NTC | NTC thermistor, mounted on cell body | 10 kΩ at 25 °C, β = 4000 K | | | | | | Per the datasheet worked example that R2/R3 are sized for |
| | — | Pull-up (STAT, INT, SCL, SDA) | 10 kΩ | | | | | | Datasheet specifies 10 kΩ |
| | — | Pull-up, generic | 1 kΩ | | | | | | |
| | D | Status LED | Green | | | | | | Driven from STAT, open drain |

---

## 5. Calculations

### 5.1 Inductor current ripple

**Equation:**

ΔI_L = {VBAT × (VIN − VBAT)} / (VIN × fs × L)

**Inputs:**

- VIN = 5.0 V (USB input)
- VBAT = 3.0 V (worst case, discharged cell)
- fs = 2.7 MHz (datasheet minimum of the 2.7–3.3 MHz spec)
- L = 1.0 µH

**Result:** ΔI_L = 0.444 A

**Datasheet reference:** Section 10.2.2.1 Inductor Selection

---

### 5.2 Inductor peak current

**Equation:**

I_PEAK = Load × (1 + ΔI_L / 2)   *(as printed — see note)*

**Note:** the printed form is dimensionally inconsistent; ΔI_L/2 is a current and cannot be added to a
dimensionless 1. The datasheet's worked example uses Load = 1 A, where both forms coincide. Correct form:

I_PEAK = Load + ΔI_L / 2

**Note on Load:** SYS sits after the inductor, so inductor average current is system load plus charge
current, not charge current alone. Load = I_SYS + I_CHG.

**Inputs:**

- ΔI_L = 0.444 A (Section 5.1)
- I_CHG = 0.500 A
- I_SYS = 0 / 0.137 / 0.462 A (idle / S2 playback / S4 peak, referred to SYS)

**Result:**

| Case | I_L average | I_PEAK |
|---|---|---|
| Charging, system idle | 0.500 A | 0.722 A |
| Charging + S2 playback | 0.637 A | 0.859 A |
| Charging + S4 peak | 0.962 A | 1.184 A |

Selected inductor (#1-063, TDK TFM141206BLE-1R0MTCA) is rated 2 A Isat and 2 A Irms, 96 mΩ max DCR.

- Worst-case average 0.962 A vs 2 A Irms — 108% margin.
- Worst-case peak 1.184 A vs 2 A Isat — 69% margin.
- At the −20% inductance tolerance limit (0.8 µH) ripple rises to 0.555 A and the S4 peak becomes
  1.24 A — 61% margin.
- The IC cycle-by-cycle current limit is 2.6 A minimum. With 2 A Isat the inductor and the IC limit are
  close enough that a saturation excursion is caught within a cycle or two rather than running away.
  Fault-case margin acceptable — item closed.
- DCR loss at the worst sustained case (0.637 A, charging while playing): 0.637² × 0.096 ≈ 39 mW.
  Charge-path only — on battery the buck is off and the BAT FET connects BAT to SYS directly, bypassing
  the inductor, so DCR has no effect on runtime.

**Note:** Isat definition (inductance drop percentage and reference temperature) to be confirmed against
the TDK characteristics table.

**Datasheet reference:** Section 10.2.2.1 Inductor Selection

---

### 5.3 Fast charge current resistor (RISET)

**Equation:**

RISET = K_ISET / I_FC = 250 / I_FC

**Inputs:** I_FC = 0.50 A (standard charge current)

**Result:** RISET = 500 Ω exactly (#1-064 is a 500 Ω 0.1% thin film part, so no E96 rounding required)

**Dissipation:** V_ISET max in regulation is 0.42 V → P = 0.42² / 500 = 0.35 mW, against a 100 mW rating.

**Note:** ISET short-circuit threshold is 45–75 Ω; 500 Ω is well clear. RISET must always be populated —
floating ISET with external ISET selected gives zero charge current.

**Datasheet reference:** Section 9.3.3 External Settings, Equation 1

---

### 5.4 Input current limit resistor (RILIM)

**Equation:**

RILIM = K_ILIM / I_IC = 270 / I_IC

**Inputs:** I_IC = 1.0 A

**Result:** RILIM = 270 Ω (#1-065)

**Dissipation:** V_ILIM max in regulation is 0.42 V → P = 0.42² / 270 = 0.65 mW, against a 100 mW rating.

**Note:** ILIM short-circuit threshold is 55 Ω typ / 75 Ω max; 270 Ω is well clear. Shorting ILIM to GND
would instead select the 2 A internal default.

**Datasheet reference:** Section 9.3.3 External Settings, Equation 2

**Note:** ILIM sets the limit on current drawn from the input source, not the current delivered to the
battery. Charge current is set separately by ISET; DPPM reduces charge current when system load plus
charge current would exceed the input limit.

**Input current required at 500 mA charge:** 500 mA × 3.7 V / (5 V × 0.90) ≈ 411 mA, before system load.
Adding an S4 system peak gives roughly 800 mA. ILIM must therefore sit above 500 mA or DPPM will throttle
charging continuously. 1 A selected.

---

### 5.5 Input voltage DPM threshold (VIN_DPM)

**Equation:**

VIN_DPM = V_REF_DPM × (R1 + R2) / R2 = 1.2 V × (R1 + R2) / R2

**Inputs:** R1 = 274 kΩ, R2 = 100 kΩ (both 1%)

**Result:** VIN_DPM = 1.2 × (374 / 100) = **4.488 V**

**What VIN_DPM does.** It is a threshold, not a regulation target. If the input voltage sags to
VIN_DPM, the charger reduces its input current draw to stop the supply collapsing further. It exists so
that a weak adapter or a poor cable degrades charging speed gracefully instead of browning out the rail.

**Rationale for 4.488 V.** The threshold has to sit below the voltage actually present at the IN pin
during normal charging, or protection engages when nothing is wrong. That voltage is lower than the
source's nominal 5 V because of losses in series with it:

| Element | Drop at ~800 mA |
|---|---|
| Source regulation droop | source-dependent, 0–150 mV |
| Cable conductors (0.033–0.4 Ω round trip) | 25–320 mV |
| Plug and receptacle contacts (~50 mΩ each pair) | ~80 mV |

So a 5.0 V source typically presents **4.7–4.8 V at IN with a good cable, and below 4.6 V with a poor
one.** That total is the "cable drop."

- At the 4.68 V default, normal operation sits only 20–120 mV above the threshold. Ordinary variation —
  a slightly longer cable, a source at the low end of its tolerance — pushes below it and DPM throttles
  charge current on a perfectly healthy setup.
- At 4.488 V, there is roughly 210–310 mV of headroom before DPM engages, so it only acts when the
  source or cable genuinely cannot support the demand.

**Floor.** 4.488 V is 138 mV above the 4.35 V recommended minimum operating VIN, so the part stays in
spec while DPM is regulating. The programmable range bottoms out at 4.2 V, which would be below that
minimum and is therefore not usable.

These are the same divider values used in the datasheet's own design example.

**Datasheet reference:** Section 9.3.3 External Settings, Equation 3; Section 10.2.2.1

**Constraints:**

- Recommended minimum operating VIN is 4.35 V — VIN_DPM must sit above this.
- VIN_DPM range is 4.2–10 V in standalone mode, 4.2–4.76 V over I2C.
- The external VDPM setting applies only in the EN2=0, EN1=1 (external ILIM) mode. The 500 mA and
  100 mA EN1/EN2 settings force VIN_DPM to 4.36 V regardless of the divider.

---

### 5.6 TS network (thermistor)

The datasheet's recommended TS network is used as published, with the thermistor chosen to match the
worked example rather than solving the threshold equations for a different part.

**Topology:** LDO → R2 → TS node; NTC and R3 both from TS node to ground.

**Values:** R2 = 5 kΩ (#1-060), R3 = 9.88 kΩ (#1-061), NTC = 10 kΩ at 25 °C with β = 4000 K.

R3 = 9.88 kΩ is the nearest available value to the datasheet's 9.82 kΩ.

**Trip thresholds** are ratiometric as a percentage of VLDO, so LDO tolerance cancels:

| Threshold | V_TS / V_LDO |
|---|---|
| V_HOT | 30.0% |
| V_WARM | 38.3% |
| V_COOL | 56.5% |
| V_COLD | 60% |

**Known deviation:** because β = 4000 K rather than 3500 K, the V_COOL and V_WARM crossings land at
approximately 13 °C and 47 °C rather than the ideal 10 °C and 45 °C. The datasheet notes this and
considers it acceptable. V_HOT and V_COLD are exact at 60 °C and 0 °C by construction.

**On a TS fault:** SYS stays active and charge current is disabled. The device continues running,
charging stops.

**Datasheet reference:** Section 9.3.10 NTC Monitor; Section 10.2.2.1 worked example

**Note:** the datasheet designates these resistors R2/R3 in Section 9.3.10 but R3/R4 in the Section
10.2.2.1 example. Same network.

---

### 5.7 USB-C input filter

**Purpose.** Attenuate the charger's 3 MHz switching current before it reaches the USB cable, where it
would otherwise radiate, and damp the hot-plug ring between cable inductance and the input capacitance.
Not a noise-floor measure for the audio chain — the charger only runs while plugged in, and LT3042
post-regulation handles conducted content regardless.

**Topology.** Pi filter from the USB-C connector to the charger IN pin:

J1 → C1 (4.7 µF) → F1 (ferrite bead) → C3 (2.2 µF, CIN) → IN, with a damping branch of R1 (0.5 Ω) in
series with C2 (4.7 µF) across the node between F1 and C3. D1 (TVS) sits at the connector, ahead of C1.

**Method.** LTspice AC analysis, with the cable modelled explicitly and swept across three cases:

| Case | R_cable (round trip) | L_cable |
|---|---|---|
| 0.5 m, 20 AWG | 33 mΩ | 250 nH |
| 1 m, 24 AWG | 170 mΩ | 500 nH |
| 2 m, thin | 400 mΩ | 1 µH |

Source modelled as 5 V behind 50 mΩ. Capacitors modelled as Murata subcircuits so ESR and ESL are
included. Load represented as 6.25 Ω, the equivalent of 800 mA at 5 V.

**Result 1 — insertion loss.** Approximately **−83 dB at 3 MHz**, with a flat passband and a corner near
50 kHz. Attenuation is dominated by the capacitance; the bead contributes roughly 3.6 Ω at 3 MHz and
earns its place above ~30 MHz, in the radiated-emissions band.

**Result 2 — output impedance (stability).** A regulating converter presents negative incremental input
resistance, approximately −6.25 Ω at this operating point. If the filter's output impedance approaches
that magnitude the combined system can oscillate (Middlebrook criterion).

Measured by injecting 1 A AC into the node with the source set to AC 0, so node voltage reads directly
as impedance.

- Without damping: peak of **46 Ω at ~900 kHz** — a pi-filter anti-resonance between the bead's
  inductance and the capacitance on either side. This fails the criterion by roughly 7×.
- With the R1/C2 damping branch: peak of **0.46 Ω**, a 100× reduction, giving better than 13× margin
  against the 6.25 Ω limit. The three cable cases converge above 300 kHz, so behaviour no longer depends
  on which cable the user plugs in.

**Conclusion.** The damping branch is load-bearing, not optional. Removing R1 and C2 restores the 46 Ω
anti-resonance and the design becomes unstable against the charger's negative input resistance.

---

### 5.8 SYS output ripple

**Equation (capacitor-only form):**

ΔV_ripple = ΔI_L × [ ESR + 1 / (8 × f_sw × C_OUT) ]

**Note:** this form omits ESL, which dominates at 3 MHz. 1 nH of package inductance presents 19 mΩ at
3 MHz — several times the ESR of a ceramic and an order of magnitude above the capacitive term. The
equation therefore under-predicts, and simulation with parasitic models is the operative method here.

**Method:** LTspice transient. SW node modelled as a PULSE source (0–5 V, 2 ns edges, 3 MHz, duty set to
V_SYS/V_IN), L = 1.0 µH with 96 mΩ DCR, capacitor bank as Murata subcircuits at the matching DC bias,
load as a DC current sink. Steady state reached before the measurement window.

**Conditions simulated:** V_IN = 5 V, duty = 0.6, load 640 mA (137 mA system + 500 mA charge), three
22 µF 1210 parts.

**Result:** V_SYS mean 2.971 V, **ripple 2.4 mV peak-to-peak** (0.08%).

**Contribution split:** the capacitive term accounts for well under 1 mV; the balance is ESR and ESL.
Capacitance value is therefore not the design variable — the number of parallel parts and their package
inductance are, since both parasitics divide by the number in parallel.

**Final arrangement:** 2 × 22 µF 1210 (#1-073) plus 1 × 0.1 µF 0402 (#1-048). The small part carries the
high-frequency content where the 1210s have gone inductive; the large parts carry bulk and transient
energy. Re-simulate to confirm this arrangement against the three-1210 result.

**Datasheet reference:** Section 7 Pin Functions (SYS — bypass locally with >20 µF); Section 12.1 Layout
Guidelines items 2 and 4

---

## 6. Register / Configuration Settings

| Register | Address | Bit(s) | Value | Effect | Reason |
|---|---|---|---|---|---|
| | | | | | |
| | | | | | |
| | | | | | |
| | | | | | |
| | | | | | |

### 6.1 Power-on defaults vs. configured state

### 6.2 Fallback behaviour

---

## 7. Thermal

| Parameter | Value | Source |
|---|---|---|
| | | |
| | | |
| | | |

**Working:**

---

## 8. Protection and Fault Behaviour

| Fault | Detection | Device response | System response |
|---|---|---|---|
| | | | |
| | | | |
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
| 4 | | | | |
| 5 | | | | |

---

## 11. Open Items

| # | Item | Blocking? | Resolved |
|---|---|---|---|
| 1 | Battery connector #1-062 is a 5.08 mm through-hole terminal block; check the envelope against the ~13.2 mm enclosure target | No | |

---

## 12. Revision Log

| Rev | Date | Change |
|---|---|---|
| 0 | 2026-09-19 | Initial issue. Part selection, external component sizing, RISET/RILIM/VIN_DPM calculations, TS network, USB-C input filter and SYS output ripple simulations. |
