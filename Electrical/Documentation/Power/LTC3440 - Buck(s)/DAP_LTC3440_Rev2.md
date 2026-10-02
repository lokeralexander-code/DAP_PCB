---
title: "Buck-Boost (LTC3440) — Working Document"
subtitle: "Digital Audio Player · Rev 2"
---

**Author:** Alexander Loker — design decisions, component selection, analysis and verification.

**Drafted by:** Claude (Anthropic) — structure and prose from the author's direction and source data.

## 1. Part Identification

| Field | Value |
|:--|:--|
| Manufacturer / MPN | Analog Devices — LTC3440EMS#TRPBF |
| Package | 10-lead MSOP (MS) |
| Function | Synchronous buck-boost, SYS → 3.9 V intermediate rail |

## 2. Design Requirements

| Requirement | Value |
|:--|:--|
| Input | SYS, 3.0 – 4.2 V |
| Output | 3.9 V |
| Load | Analog chain only (LT3042 +3.3 V, charge pump → LT3093 −3.3 V) |
| Output current | 180 mA peak, 250 mA design |
| Switching frequency | 1.2 MHz, fixed (Burst Mode off) |
| Output ripple target | 0.5% (19.5 mV pp) |
| Soft-start | Output up by ≈ 6 ms, MCU-enabled |
| Source resistance | ≈ 0.21 Ω |

**Constraints:** Low noise · Small footprint

## 3. Pin Configuration

| Pin | Name | Connection |
|:--|:--|:--|
| 1 | RT | R_T to GND |
| 2 | MODE/SYNC | GND |
| 3 | SW1 | L |
| 4 | SW2 | L |
| 5 | GND | GND |
| 6 | VOUT | +3V9, C_OUT, R1 |
| 7 | VIN | SYS, C_IN |
| 8 | SHDN/SS | R_SS from GPIO, C_SS to GND |
| 9 | FB | R1 / R2 midpoint |
| 10 | VC | C_P1 + R_Z to FB |

## 4. External Components

| Ref | Value | Rating / package | MPN | AL-PN |
|:----|:------|:--------|:----------|:---|
| L | 6.8 µH ±20% | I_sat 3.5 A (20%), 74.1 mΩ | Bourns SRP4030FA-6R8M | #1-076 |
| C_IN | 4.7 µF | 16 V X7R 0805 | Murata GRM21BR71C475KE51L | #1-071 |
| C_OUT | 22 µF | 25 V X7S 1210 | Murata GCM32EC71E226KE36L | #1-073 |
| R1 | 220 kΩ 0.1% | 0402 | YAGEO RC0402BR-07220KL | #1-074 |
| R2 | 100 kΩ 0.1% | 0603 | YAGEO RT0603BRD07100KL | #1-066 |
| R_T | 50 kΩ 0.1% | 0603 | YAGEO RT0603BRD0750KL | #1-075 |
| R_Z | 15 kΩ 0.1% | 0402 | YAGEO RT0402BRD0715KL | #1-078 |
| C_P1 | 1.5 nF ±2% | 50 V C0G 0402 | Murata GRM1555C1H152GA01D | #1-077 |
| R_SS | 54.9 kΩ 0.1% | 0402 | YAGEO RT0402BRD0754K9L | #1-079 |
| C_SS | 0.1 µF | 50 V X7R 0402 | Murata GCM155R71H104KE02D | #1-080 |
| R_PD | 1 MΩ | GPIO side of R_SS | — | — |

## 5. Equations and Results

**Switching frequency**
$$R_T = \frac{6\times10^{10}}{f} = \frac{6\times10^{10}}{1.2\times10^{6}} = 50\ \mathrm{k}\Omega$$

**Output voltage**
$$V_{OUT} = 1.22\left(1+\frac{R_1}{R_2}\right) = 1.22\left(1+\frac{220}{100}\right) = 3.904\ V\quad(3.827\text{--}3.981\ \mathrm{V})$$

**Inductor ripple** ($f$ in MHz)
$$\mathrm{Ripple}_{boost} = \frac{V_{IN(MIN)}(V_{OUT}-V_{IN(MIN)})}{f\,I_{OUT}\,L\,V_{OUT}} = \frac{3.0\times0.9}{1.2\times0.25\times6.8\times3.9} = 0.34\ (85\ \mathrm{mA_{pp}})$$
$$\mathrm{Ripple}_{buck} = \frac{V_{OUT}(V_{IN(MAX)}-V_{OUT})}{f\,I_{OUT}\,L\,V_{IN(MAX)}} = 0.14\ (34\ \mathrm{mA_{pp}})$$

**Peak inductor current** (3.0 V in, $\eta \approx 0.85$)
$$I_{L,pk} = \frac{0.25\times3.9}{3.0\times0.85} + \frac{0.085}{2} = 0.42\ A$$

**Output capacitance** (0.5% ripple)
$$C_{OUT} \geq \frac{I_{OUT}(V_{OUT}-V_{IN(MIN)})\cdot100}{\%\mathrm{Ripple}\cdot V_{OUT}^2\cdot f} = \frac{0.25\times0.9\times100}{0.5\times3.9^2\times1.2\times10^6} = 2.47\ \mu F$$

**Soft-start**
$$\tau = R_{SS}C_{SS} = 5.49\ \mathrm{ms},\qquad t_x = \tau\ln\frac{V_{GPIO}}{V_{GPIO}-V_x}$$
Enable (1.0 V): **2.0 ms** · Ramp complete (2.2 V): **6.0 ms**

**Four-switch region**
$$V_{IN} = V_{OUT}(1-150\,\mathrm{ns}\cdot f) \ \text{to}\ \frac{V_{OUT}}{1-150\,\mathrm{ns}\cdot f} = 3.20\ to\ 4.76\ V$$

## 6. Important Values

| Item | Value |
|:--|:--|
| Switch current limit | 2.7 A typ (2.5 – 2.9 A) |
| Simulated output ripple | 7.7 mV pp (target 19.5 mV) |
| Load step 150 ↔ 250 mA | ±90 mV, no ringing, 0.3 ms recovery |
| Loop crossover (implied) | ≈ 8 kHz |
| Minimum rail during step | 3.74 V |
| ESL spikes (0.5 nH, sim) | ≈ 0.3 V — layout item |

## 7. Open Problems

None.

## 8. Revision Log

| Rev | Date | Change |
|:--|:-----|:------------------------------|
| 0 | 2026-09-21 | Initial design, calculations and simulation. |
| 1 | 2026-09-21 | Schematic captured; VC-to-FB short corrected; MPN to #TRPBF. |
| 2 | 2026-09-21 | Condensed to essentials. |
