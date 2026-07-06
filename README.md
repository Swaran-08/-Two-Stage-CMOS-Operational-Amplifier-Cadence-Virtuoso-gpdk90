# Two-Stage CMOS Operational Amplifier Design using Cadence Virtuoso (gpdk90)

A fully custom two-stage CMOS Operational Amplifier designed from transistor level in **Cadence Virtuoso** using **gpdk90 technology**. The amplifier was systematically designed starting from hand calculations based on design specifications, followed by transistor sizing, small-signal analysis, stability analysis, Miller compensation, iterative optimization, and verification using DC, AC and transient simulations.

Unlike directly selecting transistor dimensions through trial and error, every transistor in this design was sized analytically from circuit specifications such as **Gain**, **Gain Bandwidth**, **Phase Margin**, **Slew Rate**, **Power Consumption**, and **Input Common Mode Range (ICMR)**.

---

# Project Objectives

The objective of this project was to design a stable two-stage CMOS Operational Amplifier capable of satisfying the following specifications while maintaining all MOSFETs in saturation.

| Parameter | Target Specification |
|-----------|---------------------:|
| Technology | gpdk90 |
| Supply Voltage | 1.8 V |
| DC Gain | ≥ 60 dB |
| Gain Bandwidth | 30 MHz |
| Phase Margin | ≥ 60° |
| Slew Rate | ≥ 20 V/µs |
| ICMR+ | 1.6 V |
| ICMR− | 0.8 V |
| Load Capacitance | 2 pF |
| Power Dissipation | ≤ 500 µW |

---

# Design Methodology

Instead of directly designing the complete two-stage amplifier, the design was carried out systematically.

```
                  Specifications
                         │
                         ▼
           Design First Stage Op-Amp
                         │
                         ▼
          Verify Gain and Device Sizing
                         │
                         ▼
      Perform Small Signal Analysis
                         │
                         ▼
    Study Stability after Adding Stage-2
                         │
                         ▼
      Understand Pole Movement Problem
                         │
                         ▼
         Introduce Miller Compensation
                         │
                         ▼
     Design Complete Two-Stage Op-Amp
                         │
                         ▼
      Size Every MOSFET Analytically
                         │
                         ▼
      Verify Gain, GBW and Phase Margin
                         │
                         ▼
       Optimize Gain and Bandwidth
                         │
                         ▼
              Final Verification
```

The entire amplifier was therefore developed in two phases.

1. Design and understand the first-stage differential amplifier.
2. Extend it into a compensated two-stage operational amplifier.

---

# Why Design the First Stage Separately?

Before designing the complete two-stage operational amplifier, the first stage was designed independently.

The objective was to understand

- Differential pair operation
- Current mirror active load
- Input Common Mode Range (ICMR)
- Slew Rate limitation
- Small-signal gain
- Transconductance sizing
- Current distribution inside the amplifier

This first-stage analysis establishes the transistor dimensions and provides insight into the limitations that arise when another gain stage is connected.

---

# First Stage Architecture

The first stage consists of

- NMOS differential pair (M1-M2)
- PMOS current mirror active load (M3-M4)
- NMOS tail current source (M5)
- Load capacitor \(C_L\)

This stage provides differential amplification while converting the differential input into a single-ended output using the PMOS current mirror.

---
# Stage-1 Design Flow

The first stage was designed analytically to determine the initial transistor dimensions before extending the circuit into a two-stage operational amplifier.

```text
Specifications
      │
      ▼
Choose Slew Rate
      │
      ▼
Calculate Tail Current (I₅)
      │
      ▼
Design M3 & M4 (ICMR+)
      │
      ▼
Small Signal Analysis
      │
      ▼
Find gm₁ from GBW
      │
      ▼
Design M1 & M2
      │
      ▼
Design M5 (ICMR−)
      │
      ▼
Verify Stage-1 Gain
```

---

# Stage-1 Design Specifications

The first-stage amplifier was designed using the following specifications.

| Parameter | Value |
|-----------|------:|
| Slew Rate | 5 V/µs |
| Load Capacitance (\(C_L\)) | 10 pF |
| ICMR+ | 1.6 V |
| ICMR− | 0.8 V |

---

# Slew Rate Analysis

The tail current was determined from the slew-rate specification.

\[
I=C\frac{dV}{dt}
\]

Hence,

\[
SR=\frac{I_5}{C_L}
\]

During the maximum positive output transition:

- M1 turns OFF.
- M2 carries the entire tail current.
- M3 also turns OFF due to the current mirror action.
- The entire tail current charges the load capacitor.

Therefore,

\[
I_5 = SR \times C_L
\]

Using

- \(SR=5~V/\mu s\)
- \(C_L=10~pF\)

the required tail current is

\[
I_5 = 50~\mu A
\]

This current is used to size all the remaining transistors of the first stage.

---

# Process Parameter Extraction

The process parameters required for hand calculations were extracted from simple DC simulations.

| Parameter | Value |
|-----------|------:|
| \(\mu_nC_{ox}\) | 316 µA/V² |
| \(\mu_pC_{ox}\) | 165 µA/V² |
| \(V_{TN}\) | ≈ 0.2 V |
| \(V_{TP}\) | ≈ 0.2 V |

These values were used throughout the complete design for transistor sizing.
