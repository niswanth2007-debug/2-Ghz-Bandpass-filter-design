# 2 GHz Bandpass Filter — ADS Simulation

> A 3rd-order Chebyshev bandpass filter centered at **2 GHz**, designed and simulated in **Keysight ADS**.  
> Covers the full design pipeline: prototype values → lumped element model → microstrip conversion → EM simulation.

---

## Specifications

| Parameter | Value |
|-----------|-------|
| Filter Type | Bandpass |
| Approximation | Chebyshev |
| Order | 3 |
| Center Frequency (f₀) | 2 GHz |
| Passband | ~1.885 – 2.115 GHz |
| Ripple | 0.5 dB (passband) |
| Port Impedance (Z₀) | 50 Ω |

---

## Design Flow

### 1. Prototype Values (Chebyshev)

Calculated using standard Chebyshev ladder network formulas:

| Element | Value |
|---------|-------|
| g₁ | 1.5963 |
| g₂ | 1.0967 |
| g₃ | 1.5963 |
| g₄ | 1.0000 |

---

### 2. Lumped Element Model

After frequency and impedance scaling (Z₀ = 50 Ω, fractional bandwidth Δ = (ω₂ - ω₁)/ω₀):

| Component | Value |
|-----------|-------|
| L₁ = L₃ | 63 nH |
| C₁ = C₃ | 0.1004 pF |
| L₂ | 0.365 nH |
| C₂ | 17.34 pF |

Topology: series LC resonators (L1-C1, L3-C3) with a shunt LC resonator (L2-C2) to ground.

---

### 3. Microstrip Conversion (Coupled Line BPF)

Converted to coupled microstrip lines using the odd/even mode impedance method.

**Substrate Parameters**

| Parameter | Value |
|-----------|-------|
| Material | FR4 |
| Thickness | 1.6 mm |
| Dielectric Constant (εr) | 4.6 |
| Conductor | Copper, 35 µm |
| Loss Tangent | 0.002 |
| Electrical Length | 90° at 2 GHz |

**Coupled Line Sections**

| Section | Zoe (Ω) | Zoo (Ω) | Width (mm) | Spacing (mm) | Length (mm) |
|---------|---------|---------|------------|--------------|-------------|
| 1 | 70.61 | 39.24 | 2.290 | 0.491 | 20.723 |
| 2 | 56.64 | 44.77 | 2.803 | 1.804 | 20.255 |
| 3 | 56.64 | 44.77 | 2.803 | 1.804 | 20.255 |
| 4 | 70.61 | 39.24 | 2.290 | 0.491 | 20.723 |

Feed lines (TL1, TL2): W = 2.918 mm, L = 5 mm

---

### 4. Optimization (ADS)

Post-conversion, a random optimizer was run in ADS to fine-tune the coupled line dimensions:

- OptimType: Random
- MaxIters: 3326
- Goals: S(1,1) minimization + S(2,1) passband flatness

Final optimized dimensions deviate slightly from hand-calculated values — see schematic for exact values.

---

### 5. EM Simulation (MoM)

Full-wave electromagnetic simulation using ADS Momentum (MoM uW):

| Parameter | Value |
|-----------|-------|
| Solver | Momentum Microwave (MoM) |
| Frequency Sweep | 1 – 3 GHz, Linear |
| Points | 200 |
| Step | ~10 MHz |

---

## Simulation Results

### Lumped Element Response
- Clean passband centered at 2 GHz
- S21 ≈ 0 dB in passband, deep rejection outside

### Microstrip Response (post-optimization)
| Marker | Frequency | S(1,1) dB | S(2,1) dB |
|--------|-----------|-----------|-----------|
| m5 | 1.885 GHz | -3.011 | -3.987 |
| m4 | 1.995 GHz | -24.253 | -0.384 |
| m3 | 2.115 GHz | -3.425 | -3.448 |

---

## Repository Structure

```
2GHz-BPF-ADS/
├── lumped/
│   └── bpf_lumped.dsn         # Lumped element schematic
├── microstrip/
│   └── bpf_microstrip.dsn     # Coupled line schematic + optimizer
├── em/
│   ├── bpf_layout.gds         # EM layout
│   └── bpf_mom.dsn            # Momentum EM setup
├── images/
│   ├── lumped_schematic.png
│   ├── lumped_response.png
│   ├── microstrip_schematic.png
│   ├── microstrip_response.png
│   ├── em_layout.png
│   └── substrate_stackup.png
├── calculations/
│   └── prototype_calculations.pdf
└── README.md
```

---

## Tools Used

- **Keysight ADS** — schematic, S-parameter simulation, optimizer, Momentum EM
- Design methodology based on Pozar — *Microwave Engineering*

---

## Author

Self-initiated RF filter design project.  
Part of an ongoing exploration of microwave filter design and simulation.
