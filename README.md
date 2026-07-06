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

**Current equation**

> I = C × (dV/dt)

Therefore,

> Slew Rate (SR) = I₅ / C_L

During the maximum positive output transition,

- M1 turns OFF.
- M2 carries the entire tail current.
- M3 also turns OFF due to current mirror action.
- The entire tail current charges the load capacitor.

Hence,

> I₅ = SR × C_L

Using

- SR = **5 V/µs**
- C_L = **10 pF**

the required tail current becomes

> **I₅ = 50 µA**

This current is used for sizing the remaining transistors of the first stage.

---

# Process Parameter Extraction

The process parameters required for transistor sizing were extracted using simple DC simulations.

| Parameter | Value |
|-----------|------:|
| μnCox | 316 µA/V² |
| μpCox | 165 µA/V² |
| VTN | ≈ 0.2 V |
| VTP | ≈ 0.2 V |

These parameters were used throughout the transistor sizing calculations.
# Stage-1 Transistor Sizing

The first-stage differential amplifier was designed analytically using the specifications listed above.

- **M3 and M4** were sized using the **ICMR+** specification to ensure saturation at the maximum common-mode input voltage.
- A **small-signal analysis** was then performed to obtain the required transconductance from the Gain Bandwidth Product (GBW).
- Using the calculated **gm₁**, the dimensions of **M1 and M2** were obtained from the MOSFET current equation.
- Finally, **M5** was designed using the **ICMR−** specification to ensure that the tail current source remained in saturation for the minimum input common-mode voltage.
- Since **M8** forms a current mirror with **M5**, both transistors were assigned identical dimensions.

The resulting transistor dimensions are shown below.

| Transistor | W/L |
|------------|----:|
| M1, M2 | 6.25 |
| M3, M4 | 7.6 |
| M5, M8 | 1.63 |

The first-stage amplifier achieved an **open-loop gain of approximately 39 dB**, confirming the correctness of the analytical sizing. However, this gain was insufficient to satisfy the target specification, motivating the addition of a second gain stage.
# Need for a Second Gain Stage

The first-stage differential amplifier achieved an open-loop gain of approximately **39 dB**, which was insufficient to satisfy the target specification of **60 dB or higher**. Therefore, a common-source amplifier was added as the second gain stage to increase the overall voltage gain.

However, directly connecting the second stage introduces a stability problem.

---

# Why Miller Compensation?

After connecting the second stage, the load capacitance of the first stage is no longer the external load capacitor. Instead, the first-stage output only sees the parasitic capacitances of the transistors, such as

- Drain-to-body capacitances
- Gate capacitance of the second-stage transistor
- Interconnect parasitic capacitances

Since these capacitances are very small, the dominant pole shifts towards higher frequencies according to

```text
Pole = 1 / (RC)
```

As a result,

- The dominant pole moves closer to the second pole.
- Phase margin decreases.
- The amplifier becomes unstable and may oscillate.

To overcome this problem, **Miller Compensation** was introduced.

A compensation capacitor (**Cc**) was connected between the output of the second stage and the output of the first stage. Due to the Miller effect, the effective capacitance seen by the first stage increases significantly, shifting the dominant pole towards lower frequencies and improving the phase margin.

In addition, the slew rate of the compensated amplifier becomes

```text
SR = I₅ / Cc
```

Thus, the Miller capacitor was selected based on both the **phase margin** and **slew rate** requirements.

---

# Design Specifications for the Two-Stage Op-Amp

The complete two-stage amplifier was designed to satisfy the following specifications.

| Parameter | Target |
|-----------|-------:|
| DC Gain | ≥ 60 dB |
| Gain Bandwidth | 30 MHz |
| Phase Margin | ≥ 60° |
| Slew Rate | ≥ 20 V/µs |
| ICMR+ | 1.6 V |
| ICMR− | 0.8 V |
| Load Capacitance | 2 pF |
| Power Dissipation | ≤ 500 µW |
| Channel Length | 500 nm |

To reduce channel length modulation and improve output resistance, the channel length of all transistors was chosen as **500 nm**, approximately twice the minimum channel length available in the technology.

Choose Cc
      │
      ▼
Find I5 from Slew Rate
      │
      ▼
Find gm1 from GBW
      │
      ▼
Size M1, M2
      │
      ▼
Size M3, M4
      │
      ▼
Size M5
      │
      ▼
Size M6
      │
      ▼
Size M7
      │
      ▼
Simulate & Optimize
# Two-Stage Op-Amp Design

The compensated two-stage operational amplifier was designed analytically by satisfying the design specifications in the following order.

```text
Choose Compensation Capacitor (Cc)
            │
            ▼
Determine Tail Current (I₅) from Slew Rate
            │
            ▼
Calculate gm₁ from Gain Bandwidth
            │
            ▼
Size M1 & M2
            │
            ▼
Size M3 & M4
            │
            ▼
Size M5
            │
            ▼
Size M6
            │
            ▼
Size M7
            │
            ▼
Verify Specifications
```

---

## Selection of Compensation Capacitor

To achieve a phase margin greater than **60°**, the compensation capacitor was chosen using the Miller compensation criterion.

Initially,

- Load Capacitance = **2 pF**
- Minimum Compensation Capacitor = **0.44 pF**

To provide sufficient stability margin,

> **Cc = 800 fF**

was selected.

---

## Tail Current Calculation

Using the slew-rate specification,

```text
SR = I₅ / Cc
```

with

- SR = **20 V/µs**
- Cc = **800 fF**

the required tail current becomes

> **I₅ = 16 µA**

Therefore,

> **I₁ = I₂ = 8 µA**

---

## Transistor Sizing

Using the calculated current and design equations, the transistor dimensions were obtained analytically.

| Transistor | Design Method | W/L |
|------------|---------------|----:|
| M1, M2 | GBW (gm calculation) | 5 |
| M3, M4 | ICMR+ | 1.185 |
| M5, M8 | ICMR− | 0.7 |
| M6 | Phase Margin (gm₆ ≥ 10gm₁) | 34 |
| M7 | Current Mirror Scaling | 10 |

The corresponding bias currents were

| Current | Value |
|---------|------:|
| I₅ | 16 µA |
| I₁ = I₂ | 8 µA |
| I₆ = I₇ | 230 µA |

After completing the analytical sizing, the complete two-stage operational amplifier was simulated to verify the DC gain, gain bandwidth, phase margin, slew rate and power dissipation.
# Simulation Results and Design Optimization

After implementing the analytically calculated transistor dimensions, the complete two-stage operational amplifier was simulated under two input common-mode conditions.

## Initial Performance

| Parameter | VCM = 1.6 V | VCM = 0.8 V |
|-----------|------------:|------------:|
| DC Gain | 54.05 dB | 64.50 dB |
| First Stage Gain | 25.66 dB | - |
| Second Stage Gain | 28.39 dB | - |

Although the amplifier satisfied the bandwidth and stability requirements, the overall gain was still below the target value of **60 dB** under all operating conditions.

---

# Gain Optimization

From the small-signal analysis, it was observed that the **second stage** was limiting the overall gain due to its lower output resistance.

To increase the gain,

- The **channel length of M7** was increased.
- The **width was increased proportionally** to keep the **W/L ratio constant**.
- Increasing the channel length reduced **channel length modulation**, thereby reducing **gds** and increasing the output resistance.

Similarly, the channel lengths of **M5** and **M8** were also increased while maintaining their original W/L ratios.

This optimization improved the second-stage gain without affecting the operating point of the amplifier.

---

## Performance After Channel Length Optimization

| Parameter | VCM = 1.6 V | VCM = 0.8 V |
|-----------|------------:|------------:|
| DC Gain | 62.30 dB | 66.63 dB |
| Gain Bandwidth | 30 MHz | 30 MHz |
| Phase Margin | 64° | 64° |

The gain specification was now successfully achieved while maintaining the required bandwidth and stability.

---

# Further Gain Improvement

To further improve the open-loop gain, the dimensions of the differential input pair (**M1 and M2**) were modified.

The channel length was increased while maintaining the same W/L ratio by proportionally increasing the width.

This increased the output resistance of the first stage and resulted in an additional improvement in the overall gain.

Attempts were also made to modify the dimensions of **M3 and M4**. However, this caused some transistors to leave the saturation region. Therefore, the previous dimensions were retained to ensure proper operation.

---

# Bandwidth Optimization

The initial compensation capacitor was chosen as

> **Cc = 800 fF**

to provide sufficient phase margin.

After satisfying the stability requirement, the compensation capacitor was reduced to

> **Cc = 600 fF**

Reducing the Miller capacitance increased the Gain Bandwidth Product while maintaining an acceptable phase margin.

---

# Power Dissipation

The total power consumption was calculated using the final bias currents.

| Parameter | Value |
|-----------|------:|
| Power Dissipation | 471.6 µW |

The designed amplifier satisfies the required power specification.

---

# Final Design Parameters

| Parameter | Final Value |
|-----------|------------:|
| DC Gain | 71.72 dB |
| Gain Bandwidth | 30 MHz |
| Phase Margin | 62.5° |
| Slew Rate | 26 V/µs |
| Load Capacitance | 2 pF |
| Compensation Capacitor | 600 fF |
| Power Dissipation | 471.6 µW |

---

# Final Transistor Dimensions

| Transistor | W/L |
|-----------|----:|
| M1, M2 | 5 |
| M3, M4 | 1.185 |
| M5, M8 | 0.7 |
| M6 | 34 |
| M7 | 10 |

---

# Simulation Results

The final design was verified using the following simulations in Cadence Virtuoso.

- DC Operating Point Analysis
- AC Analysis
- Phase Margin Analysis
- Transient Analysis
- Gain Verification
- Slew Rate Measurement
- Power Consumption Analysis

The amplifier successfully met the target specifications after iterative optimization while ensuring that all transistors remained in the saturation region.

---

# Repository Contents

```
├── README.md
├── Calculations.pdf
├── Cadence Schematic
├── Symbol
├── Testbench
├── AC Analysis Results
├── DC Analysis Results
├── Transient Results
├── Final Simulation Plots
└── Design Report
```

---

# Tools Used

- Cadence Virtuoso
- gpdk90 Technology Library
- Analog Design Environment (ADE)
- Spectre Simulator

---

# References

1. Design of Analog CMOS Integrated Circuits – Behzad Razavi
2. CMOS Analog Circuit Design – Philip E. Allen & Douglas R. Holberg
3. Sedra & Smith – Microelectronic Circuits
