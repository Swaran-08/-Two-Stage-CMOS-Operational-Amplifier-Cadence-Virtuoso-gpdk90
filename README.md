# Two-Stage CMOS Operational Amplifier using Cadence Virtuoso (gpdk90)

A fully custom two-stage CMOS Operational Amplifier was designed at the transistor level in **Cadence Virtuoso** using the **gpdk90 technology library**. The design follows a systematic analog design methodology, beginning with hand calculations and analytical transistor sizing, followed by simulation-based verification and iterative optimization.

Unlike trial-and-error sizing, every MOSFET in this design was dimensioned analytically using the required specifications for gain, bandwidth, stability, slew rate, input common-mode range and power consumption.

---

# Design Specifications

| Parameter | Specification |
|-----------|--------------:|
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

Instead of directly designing a two-stage operational amplifier, the design was divided into two phases.

1. Design and analyze the first-stage differential amplifier.
2. Extend it into a compensated two-stage operational amplifier.

The overall design flow is shown below.

```text
Design Specifications
        │
        ▼
Design First Stage
        │
        ▼
Verify Gain & Saturation
        │
        ▼
Small Signal Analysis
        │
        ▼
Study Pole Locations
        │
        ▼
Introduce Miller Compensation
        │
        ▼
Design Complete Two-Stage Op-Amp
        │
        ▼
Optimize Gain & Stability
        │
        ▼
Final Verification
```

---

# First-Stage Differential Amplifier

The first stage consists of

- NMOS Differential Pair (M1-M2)
- PMOS Current Mirror Load (M3-M4)
- NMOS Tail Current Source (M5)
- Load Capacitor (CL)

Designing the first stage independently provides the initial transistor dimensions and helps in understanding the operation of the differential amplifier before extending it into a complete operational amplifier.

The first-stage design focused on

- Slew Rate
- Input Common Mode Range (ICMR)
- Small Signal Gain
- Gain Bandwidth Product
- Analytical transistor sizing

---

# Stage-1 Design Flow

```text
Choose Slew Rate
      │
      ▼
Find Tail Current
      │
      ▼
Design M3 & M4 (ICMR+)
      │
      ▼
Small Signal Analysis
      │
      ▼
Find gm from GBW
      │
      ▼
Design M1 & M2
      │
      ▼
Design M5 (ICMR−)
      │
      ▼
Verify First Stage Gain
```

---

# Stage-1 Design
- [1st Stage Schematic](images/1st_stage_schematic.png)
The first-stage amplifier was designed using the following specifications.

| Parameter | Value |
|-----------|------:|
| Slew Rate | 5 V/µs |
| Load Capacitance | 10 pF |
| ICMR+ | 1.6 V |
| ICMR− | 0.8 V |

The tail current was determined from

```text
SR = I5 / CL
```

which resulted in

> **I5 = 50 µA**

The required process parameters were extracted from DC simulations.

| Parameter | Value |
|-----------|------:|
| μnCox | 316 µA/V² |
| μpCox | 165 µA/V² |
| VTN | ≈ 0.2 V |
| VTP | ≈ 0.2 V |

Using the MOSFET current equations and saturation conditions,

- **M3 & M4** were sized using the **ICMR+** specification.
- **M1 & M2** were sized using the required transconductance obtained from the Gain Bandwidth equation.
- **M5** was designed using the **ICMR−** specification.
- **M8** was chosen identical to **M5** since both form a current mirror.

### Stage-1 Transistor Dimensions

| Transistor | W/L |
|-----------|----:|
| M1, M2 | 6.25 |
| M3, M4 | 7.6 |
| M5, M8 | 1.63 |
- [1st Stage Gain](images/1st_stage_gain.png)
The designed first-stage amplifier achieved an open-loop gain of approximately **39 dB**, which was insufficient for the target specification. Therefore, a second gain stage was introduced.
# Need for a Second Gain Stage

The first-stage differential amplifier provided an open-loop gain of approximately **39 dB**. Although the analytical sizing was verified through simulation, the gain was insufficient to meet the target specification of **60 dB**. Therefore, a **common-source amplifier** was added as the second stage to provide additional voltage gain.

However, directly cascading the two stages introduces a stability issue.

---

# Why Miller Compensation?

When the second stage is connected directly, the first-stage output no longer drives the external load capacitor. Instead, it only sees the small parasitic capacitances of the MOS transistors, such as

- Drain-to-body capacitances
- Gate capacitance of M6
- Interconnect parasitic capacitances

Since these capacitances are very small, the dominant pole shifts towards higher frequencies.

```text
Pole = 1 / RC
```

As the dominant pole moves closer to the second pole,

- Phase Margin decreases.
- The amplifier becomes unstable.
- Oscillations may occur.

To improve stability, **Miller Compensation** was introduced by connecting a compensation capacitor (**Cc**) between the output of the first stage and the output of the second stage.

This increases the effective capacitance seen by the first stage, shifts the dominant pole towards lower frequencies, separates the poles, and significantly improves the phase margin.

After Miller compensation, the slew rate becomes

```text
SR = I5 / Cc
```

Thus, the compensation capacitor was selected to satisfy both the **phase margin** and **slew-rate** requirements.

---

# Design Specifications for the Two-Stage Op-Amp
- [2 Stage Schematic](images/2_stage_schematic.png)
The complete two-stage operational amplifier was designed using the following specifications.

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

To minimize channel length modulation and improve output resistance, the channel length of all transistors was chosen as **500 nm**, approximately twice the minimum channel length available in gpdk90.

---

# Two-Stage Design Flow

The complete amplifier was designed analytically using the following sequence.

```text
Select Compensation Capacitor (Cc)
               │
               ▼
Calculate Tail Current (I5)
               │
               ▼
Find gm1 from GBW
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
Verify Gain, GBW & Phase Margin
```

---

# Two-Stage Transistor Sizing

The minimum compensation capacitor required for a **phase margin greater than 60°** was calculated to be approximately **0.44 pF**. To provide sufficient stability margin,

> **Cc = 800 fF**

was selected.

Using

```text
SR = I5 / Cc
```

the required tail current was obtained as

> **I5 = 16 µA**

Hence,

| Current | Value |
|---------|------:|
| I5 | 16 µA |
| I1 = I2 | 8 µA |

Using the design equations, saturation conditions, GBW equation and ICMR constraints, the transistor dimensions were obtained analytically.

| Transistor | Design Basis | W/L |
|------------|--------------|----:|
| M1, M2 | Gain Bandwidth | 5 |
| M3, M4 | ICMR+ | 1.185 |
| M5, M8 | ICMR− | 0.7 |
| M6 | Phase Margin (gm₆ ≥ 10gm₁) | 34 |
| M7 | Current Scaling | 10 |

The corresponding second-stage bias currents were

| Current | Value |
|---------|------:|
| I6 = I7 | 230 µA |

After completing the analytical sizing, the complete two-stage operational amplifier was simulated and verified using DC, AC and transient analyses before performing further optimization.
# Simulation Results

The analytically calculated transistor dimensions were verified through AC, DC and transient simulations.

### Initial Results

| Parameter | VCM = 1.6 V | VCM = 0.8 V |
|-----------|------------:|------------:|
| DC Gain | 54.05 dB | 64.50 dB |
| First Stage Gain | 25.66 dB | 34.32dB |
| Second Stage Gain | 28.39 dB | 30.1dB |

The amplifier satisfied the bandwidth and stability requirements, but the gain at **VCM = 1.6 V** was still below the target value.

---

# Design Optimization

From the small-signal analysis, the **second stage** was identified as the limiting factor for the overall gain.

To improve the gain,

- The channel lengths of **M7**, **M5** and **M8** were increased.
- The widths were increased proportionally to maintain the same **W/L ratio**.
- Increasing the channel length reduced **channel length modulation**, increasing the output resistance and consequently the voltage gain.

After optimization,
- [Final Amplified Signal](images/final_amplified_signal.png)
- [Final Gain and Phase Margin](images/final_gain_and_phasemargin.png)
  
| Parameter | VCM = 1.6 V | VCM = 0.8 V |
|-----------|------------:|------------:|
| DC Gain | 62.30 dB | 66.63 dB |
| Gain Bandwidth | 25 MHz | 25 MHz |
| Phase Margin | 68° | 68° |

To further improve the gain, the channel length of **M1** and **M2** was also increased while maintaining the same W/L ratio. Attempts to modify **M3** and **M4** caused some transistors to leave saturation; therefore, their dimensions were retained.
# Bandwidth Optimization
- [Compensation Capacitor (800 fF)](images/cc_is800fF_PM_68deg.png)
- [Compensation Capacitor (600 fF)](images/cc_is600fF_Phasemargin_63deg.png)

Initially,

> **Cc = 800 fF**

was selected to satisfy the required phase margin.

After confirming stable operation, the compensation capacitor was reduced to

> **Cc = 600 fF**

This increased the Gain Bandwidth Product to 30 MHz while maintaining the required phase margin.

---

# Power Dissipation

| Parameter | Value |
|-----------|------:|
| Power Dissipation | 471.6 µW |

---

# Final Performance

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

# Simulations Performed

- DC Analysis
- AC Analysis
- Phase Margin Analysis
- Transient Analysis
- Slew Rate Measurement
- Power Analysis

The final two-stage CMOS operational amplifier successfully met all the target specifications after iterative optimization while maintaining every transistor in saturation.

---

# Tools Used

- Cadence Virtuoso
- gpdk90 Technology Library
- Analog Design Environment (ADE)
- Spectre Simulator

---
