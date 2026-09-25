---
title: "Charger (BQ24250) — Working Document"
subtitle: "Digital Audio Player · Rev 2"
---

**Author:** Alexander Loker — design decisions, component selection, analysis and verification.

**Drafted by:** Claude (Anthropic) — structure and prose from the author's direction and source data.

## 1. Part Identification

| Field | Value |
|:--|:--|
| Manufacturer / MPN | Texas Instruments — BQ24250RGET |
| AL-PN | #1-054 |
| Package | 24-VQFN (4 × 4) |
| Function | 3 MHz switch-mode Li-ion charger with PowerPath |

## 2. Design Requirements

| Requirement | Value |
|:--|:--|
| Input | USB-C, 5 V |
| Battery | 1S Li-Po, 3.0 – 4.2 V, 1850 mAh |
| Charge current | 500 mA (0.27C) |
| Input current limit | 1 A |
| VIN_DPM | 4.488 V |
| Switching frequency | 3 MHz (2.7 – 3.3 MHz) |
| Charge temperature window | TS network, 0 – 60 °C |

**Constraints:** Low noise · Small footprint

## 3. Pin Configuration

| Pin | Name | Connection |
|:--|:--|:--|
| 1 | CE | 10 kΩ pull-up to +3V3 |
| 2 | EN1 | 10 kΩ pull-up to +3V3 |
| 3 | EN2 | 10 kΩ pull-down to GND |
| 4 | AGND | Ground |
| 5 | SDA | 10 kΩ pull-up to +3V3 |
| 6 | SCL | 10 kΩ pull-up to +3V3 |
| 7 | STAT | Open drain — no pull-up |
| 8 | INT | 10 kΩ pull-up to +3V3 |
| 9 | TS | R2 from LDO, R3 + NTC to AGND |
| 10 | ISET | RISET to AGND |
| 11, 12 | BAT | BAT+, CBAT |
| 13, 14 | SYS | SYS, CSYS bank |
| 15, 16 | PGND | GND |
| 17, 18 | SW | L, CBOOT |
| 19 | IN | VUSB (filtered), CIN, VDPM divider |
| 20 | PMID | CPMID to GND |
| 21 | BOOT | CBOOT to SW |
| 22 | ILIM | RILIM to AGND |
| 23 | VDPM | Divider tap |
| 24 | LDO | CLDO, top of TS network |
| 25 | Thermal pad | GND |

## 4. External Components

| Ref | Value | Rating / package | MPN | AL-PN |
|:----|:------|:--------|:----------|:---|
| L | 1.0 µH ±20% | 2 A I_sat, 96 mΩ, 0505 | TDK TFM141206BLE-1R0MTCA | #1-063 |
| CBAT, CLDO, CPMID | 1 µF | 16 V X6S 0402 | Murata GRM155C81C105KE11D | #1-058 |
| CBOOT | 33 nF | 16 V X7R 0402 | Murata GRM155R71C333KA01D | #1-059 |
| CIN (C3) | 2.2 µF | 25 V X7R 0603 | Murata GRM188Z71E225KE43D | #1-070 |
| CSYS1, CSYS2 | 22 µF | 25 V X7S 1210 | Murata GCM32EC71E226KE36L | #1-073 |
| CSYS3 | 0.1 µF | 50 V X7R 0402 | Murata GRM155R71H104KE14D | #1-048 |
| RISET | 500 Ω 0.1% | 0603 | YAGEO RT0603BRC07500RL | #1-064 |
| RILIM | 270 Ω 1% | 0603 | YAGEO RC0603FR-07270RL | #1-065 |
| R_DPM1 / R_DPM2 | 274 kΩ / 100 kΩ 1% | 0603 | Generic | — |
| R2 (TS) | 5 kΩ 1% | 0402 | YAGEO RC0402FR-075KL | #1-060 |
| R3 (TS) | 9.88 kΩ 0.5% | 0402 | YAGEO RT0402DRD079K88L | #1-061 |
| NTC | 10 kΩ, β 4000 K | On cell body | — | — |
| F1 | 120 Ω @ 100 MHz | 3 A, 0805 | Würth 742792023 | #1-068 |
| C1, C2 (filter) | 4.7 µF | 16 V X7R 0805 | Murata GRM21BR71C475KE51L | #1-071 |
| R1 (filter damping) | 0.5 Ω 1% | 0402 | YAGEO RL0402FR-070R5L | #1-072 |
| D1 | SMAJ7.0A TVS | SMA | Diodes Inc. SMAJ7.0A-13-F | #1-049 |
| J1 | USB-C receptacle | USB 2.0 | GCT USB4085-GF-A | #1-069 |
| J | Battery connector, 3-pos | 5.08 mm TH | Phoenix Contact 1786417 | #1-062 |
| R_CC1, R_CC2 | 5.1 kΩ | 0402 | Generic | — |
| Pull-ups / pull-down | 10 kΩ (×5 / ×1) | 0603 | Generic | — |

## 5. Equations and Results

**Inductor ripple** (VIN 5 V, VBAT 3.0 V, $f_s$ 2.7 MHz, L 1.0 µH)
$$\Delta I_L = \frac{V_{BAT}(V_{IN}-V_{BAT})}{V_{IN}\,f_s\,L} = 0.444\ A$$

**Inductor peak current** (Load = $I_{SYS} + I_{CHG}$)
$$I_{PEAK} = I_{Load} + \frac{\Delta I_L}{2}$$

| Case | I_avg | I_PEAK |
|:--|:--|:--|
| Charging, idle | 0.500 A | 0.722 A |
| Charging + S2 playback | 0.637 A | 0.859 A |
| Charging + S4 peak | 0.962 A | **1.184 A** (vs 2 A I_sat) |

**Charge current**
$$R_{ISET} = \frac{250}{I_{FC}} = \frac{250}{0.5} = 500\ \Omega$$

**Input current limit**
$$R_{ILIM} = \frac{270}{I_{IC}} = \frac{270}{1.0} = 270\ \Omega$$

**Input DPM threshold**
$$V_{IN\_DPM} = 1.2\,\frac{R_1+R_2}{R_2} = 1.2\times\frac{374}{100} = 4.488\ V$$

**TS thresholds** (ratiometric to $V_{LDO}$; 10 kΩ β 4000 K NTC)

| Threshold | V_TS / V_LDO | Temperature |
|:--|:--|:--|
| Cold | 60% | 0 °C |
| Cool | 56.5% | ≈ 13 °C |
| Warm | 38.3% | ≈ 47 °C |
| Hot | 30% | 60 °C |

## 6. Important Values

| Item | Value |
|:--|:--|
| Input filter insertion loss | −83 dB at 3 MHz |
| Filter output impedance peak | 0.46 Ω with damping (46 Ω without); limit 6.25 Ω |
| SYS ripple (sim) | 2.4 mV pp |
| Inductor DCR loss (charging + playback) | ≈ 39 mW |
| Battery FET R_ON(BAT-SYS) | 30 mΩ typ / 40 mΩ max |
| IC current limit | 2.6 A min |

## 7. Open Problems

| # | Problem |
|:-|:------------------------------|
| 1 | RISET shows 50 Ω on schematic — library Value field must read 500 Ω |
| 2 | STAT has no pull-up |
| 3 | CE pulled up to +3V3 — charging disabled until firmware drives it low; defeats standalone fallback |
| 4 | VDPM upper resistor 270 kΩ on schematic vs 274 kΩ calculated |
| 5 | USB-C D+/D− (DP1/DP2, DN1/DN2) not connected — needed for USB MSC |
| 6 | AGND / GND — confirm single net or deliberate tie |
| 7 | I2C may only work while USB is present (powered from IN) — verify before driver |
| 8 | NTC return shares battery return on J pin 3 |
| 9 | Battery connector height vs 13.2 mm enclosure |
| 10 | Cell limits charging to 0.2C (380 mA) at 0 – 15 °C and forbids charging above 50 °C; charger runs 500 mA until ≈ 13 °C and charges to 60 °C |
| 11 | Filter R1 pulse rating during hot plug |
| 12 | Re-simulate SYS ripple with final 2 × 22 µF + 0.1 µF bank |

## 8. Revision Log

| Rev | Date | Change |
|:--|:-----|:------------------------------|
| 0 | 2026-09-19 | Initial issue: part selection, sizing, calculations, TS network, input filter and SYS ripple simulations. |
| 1 | 2026-09-19 | Reviewed against schematic BQ-CHARGER-V1; pin connections and schematic findings added. |
| 2 | 2026-09-21 | Low noise and small footprint constraints added; cell charge-temperature mismatch added; condensed to essentials. |
