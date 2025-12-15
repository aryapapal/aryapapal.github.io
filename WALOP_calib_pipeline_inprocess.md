# WALOP CALIBRATION MODEL: COMPLETE DETAILED EXPLANATION


---

## SECTION 1: COMPLETE MUELLER MATRIX FORMULATION

### 1.1 Full System Mueller Matrix Chain (from sky to measurement)

The light path through WALOP can be decomposed into sequential Mueller matrix transformations. Each optical element modifies the polarization state through matrix multiplication.

**Complete mathematical chain:**

For the Q-arm (Arm 1):
```
S_measured^(1) = M_WP1 × M_HWP1(θ_inc, T) × M_BK7 × S_sky
```

For the U-arm (Arm 2):
```
S_measured^(2) = M_WP2 × M_HWP2(θ_inc, T) × M_BK7 × S_sky
```

Where:
- S_sky = [I, Q, U, V]^T (input Stokes vector from star)
- M_BK7 = Mueller matrix of beam-splitting wedge
- M_HWP1, M_HWP2 = Mueller matrices of fixed HWPs in each arm (field-dependent)
- M_WP1, M_WP2 = Mueller matrices of Wollaston prisms
- θ_inc(x,y) = angle of incidence at field point (x,y)
- T = temperature (affects birefringence Δn)

**Physical meaning:** Each Mueller matrix is a 4×4 transformation that describes how that optical element distorts the input polarization state. By multiplying them sequentially, we get the total instrument response.

### 1.2 Input Stokes Vector (Sky Source)

An arbitrary polarized star can be described by its Stokes vector:

```
S_sky = [I]       = [I_0(1 + p cos 2ψ)]
        [Q]         [p cos 2ψ        ]
        [U]         [p sin 2ψ        ]
        [V]         [0               ]
```

Where:
- **I** = total intensity (brightness)
- **Q** = linear polarization along meridian (0° E-W, 180° N-S)
- **U** = linear polarization along diagonal (45° NE-SW)
- **V** = circular polarization (usually ~0 for stellar light)
- **I_0** = reference intensity
- **p** ∈ [0,1] = degree of linear polarization (0 = unpolarized, 1 = perfectly polarized)
- **ψ** = position angle (EVPA) measured East from North

**Key insight:** The Stokes vector is a complete mathematical description of the polarization state. Any optical element that changes polarization does so by rotating and mixing these four components.

### 1.3 Mueller Matrix of BK7 Wedge Beam Splitter

The BK7 wedge has two main functions:
1. Split the incoming beam 50-50 into two parallel arms
2. Introduce small stress-induced birefringence and differential transmission

**Ideal Mueller (perfect 50-50 split, no birefringence):**

```
M_BK7_ideal = [1/2,  0,    0,    0  ]
              [0,    1/2,  0,    0  ]
              [0,    0,    1/2,  0  ]
              [0,    0,    0,    1/2]
```

This simply attenuates all Stokes components by 50% (beam split factor).

**Real Mueller (with stress effects):**

```
M_BK7_real = [1/2,  0,           0,           0  ]
             [0,    1/2,         ε_BK7,       0  ]
             [0,    ε_BK7,       1/2,         0  ]
             [0,    0,           0,           1/2]
```

Where **ε_BK7 ≈ 0.002 to 0.01** represents small Q↔U mixing from:
- Stress-induced birefringence in the coating layers
- Asymmetric transmission of the two orthogonal polarizations
- Small retardance introduced by the wedge geometry itself

**Physical interpretation:** The off-diagonal ε_BK7 terms in rows/columns 2 and 3 mean that some of the input Q polarization "leaks" into the U direction, and vice versa. This is small but measurable.

**After multiplication with input:**
```
S_after_BK7 = [I_0/2 (1 + p cos 2ψ)     ]
              [p cos 2ψ / 2 + ε_BK7 × (p sin 2ψ)/2]   ← Q component + leakage
              [p sin 2ψ / 2 + ε_BK7 × (p cos 2ψ)/2]   ← U component + leakage
              [0                        ]
```

The wedge has degraded the polarization purity slightly by mixing Q and U.

### 1.4 Mueller Matrix of Fixed HWP1 at 0° (Q-arm)

The first fixed HWP is oriented at 0° to measure the Q Stokes parameter.

**Ideal Mueller at normal incidence (0°):**

An ideal half-wave plate (HWP) at 0° orientation has retardance δ = π (180°). Its Mueller matrix is:

```
M_HWP1_ideal(0°) = [1,  0,          0,           0]
                   [0,  cos(4×0°),  sin(4×0°),   0]
                   [0, -sin(4×0°),  cos(4×0°),   0]
                   [0,  0,          0,           1]

Substituting 4×0° = 0:

M_HWP1_ideal(0°) = [1,  0,  0,  0]
                   [0,  1,  0,  0]
                   [0,  0, -1,  0]
                   [0,  0,  0,  1]
```

**Physical meaning:** 
- Row/column 1 (Intensity I): unchanged by HWP (no depolarization at normal incidence)
- Rows/columns 2-3 (Q,U): Q passes through unchanged (1), U gets flipped sign (-1) due to π retardance
- Row/column 4 (V): circular polarization unchanged (HWP only affects linear)

So at normal incidence, the HWP acts as a **pure analyzer of Q** with no cross-talk to U.

**Real Mueller at oblique incidence θ_inc(x,y):**

When light hits the HWP at an oblique angle (not perpendicular to the surface), several things happen:

1. **Longer optical path:** The ray travels distance t/cos(θ_inc) instead of t through the crystal, accumulating extra retardance.

2. **Misaligned basis:** The fast/slow axes of the crystal are defined in the crystal's reference frame, but the ray's polarization is defined in the lab frame (Q along E-W, U along NE-SW). At an angle, these basis systems are no longer perfectly aligned → cross-talk.

3. **Projection effects:** The component of polarization along the oblique ray direction changes → Q and U components see different effective retardances.

The real Mueller becomes:

```
M_HWP1_real(x,y,T) ≈ [1,                                  0,              0,    0]
                      [0,  1 - C_HWP1(x,y,T),    ε_HWP1(x,y,T),  0]
                      [0, -ε_HWP1(x,y,T),  -(1 - C_HWP1(x,y,T)), 0]
                      [0,  0,                     0,            1]
```

Where **C_HWP1(x,y,T)** is the total cross-talk coefficient arising from multiple sources:

```
C_HWP1(x,y,T) = C_oblique(θ_inc) 
               + C_stress(r) 
               + C_surface(x,y) 
               + C_misalign 
               + C_temp(T)
```

**Breaking down each contribution:**

**a) Oblique incidence contribution:**
```
C_oblique(θ_inc) = k_HWP × sin²(θ_inc(x,y))

where k_HWP ≈ 1 (numerical factor from Mueller theory)
and θ_inc(x,y) = atan(√(x² + y²) / f_eff)

At center (x,y) = (0,0): θ_inc = 0° → C_oblique ≈ 0.001
At edge (x,y) = (17.4", 17.4"): θ_inc ≈ 20° → C_oblique ≈ 0.05

This is the DOMINANT contribution (~70% of total C).
```

**Physical mechanism (detailed):**
- The HWP crystal has perpendicular fast and slow axes with retardance difference Δn × t.
- At normal incidence, this retardance is exactly π (90° phase shift between axes).
- At oblique θ_inc, the optical path becomes t/cos(θ_inc) ≈ t(1 + θ_inc²/2).
- Extra retardance: δ_extra ≈ (2π/λ) × Δn × t × (θ_inc²/2).
- This extra phase shifts the Q and U components by different amounts → Q→U leakage.
- The magnitude of leakage goes as sin²(θ_inc) from the projection geometry.

**b) Stress-induced birefringence contribution:**
```
C_stress(r) = k_stress × (r/r_clamp)^2

where r = √(x² + y²) = distance from center (in focal plane)
      r_clamp ≈ 50 mm = radius where mechanical clamp touches HWP
      k_stress ≈ 0.01 to 0.02 (from photoelastic effect)

At center: C_stress ≈ 0.001
At edge (r_max ≈ 25 mm): C_stress ≈ 0.01

Physical mechanism:
- The HWP is held in a mechanical mount that exerts radial pressure.
- This stress creates additional birefringence (photoelastic effect): δΔn ∝ σ
- Stress is highest near the clamp, lowest at center.
- Creates a radial pattern of retardance variation → field-dependent leakage.
```

**c) Surface thickness variation contribution:**
```
C_surface(x,y) = k_surf × δt(x,y) / t_nominal

where δt(x,y) = local thickness deviation (manufacturing tolerance)
      typical: |δt| ~ 1-5 μm over a 50 mm diameter plate
      t_nominal ~ 2-3 mm

Magnitude: C_surface ~ 0.002 to 0.005

Physical mechanism:
- HWP thickness is never perfectly uniform.
- Retardance ∝ thickness, so δt → δ(retardance)
- Creates local variations in the degree of leakage.
- Pattern is determined by manufacturing (often has residual stress-induced ripples).
```

**d) Fast-axis misalignment contribution:**
```
C_misalign = sin(2 × δφ) × [coupling factor]

where δφ = deviation of HWP fast axis from nominal 0° orientation
      typical: |δφ| ~ 1-3°

For δφ = 1.5°:
C_misalign = sin(2 × 1.5°) = sin(3°) ≈ 0.05

Physical mechanism:
- The HWP is designed to have fast axis along the instrument's Q reference.
- If the HWP is rotated relative to the mount (due to installation error, thermal expansion mismatch, etc.), the fast/slow axes are no longer aligned to Q/U.
- When linearly polarized light enters, it must be decomposed into the misaligned fast/slow basis.
- This creates an immediate Q↔U coupling.
- Unlike oblique incidence, this effect is GLOBAL (same everywhere on the HWP).
```

**e) Temperature drift contribution:**
```
C_temp(T) = k_temp × (T - T_ref)

where dΔn/dT ≈ -10^-5 K^-1 (for quartz, typical HWP material)
      k_temp ≈ 0.0001 to 0.001 K^-1 (depends on λ and plate thickness)

For ΔT = 10 K (night sky cooling):
C_temp ≈ 0.001 to 0.01

Physical mechanism:
- Birefringence Δn(T) varies with temperature (material property).
- Temperature change → retardance change → effective leakage changes.
- This is TIME-DEPENDENT during night observations.
- Calibration done at T_cal becomes stale if temperature drifts to T_sci.
```

**Total cross-talk at each grid point and time:**

```
C_HWP1(x,y,T) ≈ 0.05 × sin²(θ_inc(x,y))  [oblique, ~70% of total]
               + 0.01 × (r(x,y)/50mm)²    [stress, ~15% of total]
               + 0.003 (random pattern)    [surface, ~5% of total]
               + 0.05                      [misalign, ~10% global offset]
               + 0.0005 × (T - T_ref)      [temp, ~depends on dT]

Example at edge (θ_inc=20°, r=25mm):
C_HWP1(edge) = 0.05×0.12 + 0.01×0.25 + 0.003 + 0.05 + 0.0005×ΔT
             ≈ 0.006 + 0.0025 + 0.003 + 0.05 + ε_T
             ≈ 0.06 to 0.08 (roughly 6-8% leakage at edges)

At center (θ_inc=0°, r=0):
C_HWP1(center) = 0 + 0 + 0.003 + 0.05 + 0.0005×ΔT
               ≈ 0.053 (roughly 5%, mostly misalignment)
```

### 1.5 Mueller Matrix of Wollaston Prism (WP1)

The Wollaston prism (WP) is a birefringent optical element that splits a beam into two orthogonally polarized components (ordinary 'o' and extraordinary 'e'):

**Ideal Wollaston Mueller:**

```
M_WP1_ideal = [1,  0,  0,  0]
              [0,  1,  0,  0]
              [0,  0, -1,  0]
              [0,  0,  0,  1]
```

**Physical meaning:**
- The ordinary beam preferentially transmits horizontal polarization (Q component).
- The extraordinary beam preferentially transmits vertical polarization (U component).
- Ideally, no cross-talk between the two beams.

**Real Wollaston Mueller (with small cross-pol):**

```
M_WP1_real ≈ [1,  0,      0,      0]
             [0,  1,     ε_WP,    0]
             [0, ε_WP,   -1,      0]
             [0,  0,      0,      1]
```

Where **ε_WP ≈ 10^-4 to 10^-3** (very small, typically negligible).

**Sources of WP imperfection:**
- Fabrication errors in crystal growth
- Coating reflections on surfaces
- Alignment errors in the optical mount
- Wavelength dependence (chromatic effects across filter bandpass)

**Why it's negligible:** Modern Wollaston prisms are manufactured to high precision. The extinction ratio (ability to separate ordinary and extraordinary) is typically > 10,000:1, meaning only 0.01% cross-pol. This is much smaller than the HWP cross-talk.

### 1.6 Combined Mueller Matrix per Arm (Q-arm example)

Multiply the three Mueller matrices in sequence:

```
M_total^(1) = M_WP1 × M_HWP1 × M_BK7
```

After working through the matrix multiplication (retaining only the 4×4 Stokes structure):

```
M_total^(1)(x,y,T) ≈ [1/2,    0,                    0,              0    ]
                     [0,      1/2,                 -C₁(x,y,T),     0    ]
                     [0,     -C₁(x,y,T),          -1/2,            0    ]
                     [0,      0,                    0,              1/2  ]
```

**Interpretation of entries:**

- **(1,1) = 1/2:** Total intensity is halved (beam split).
- **(2,2) = 1/2:** Q component passes with 50% transmission.
- **(3,3) = -1/2:** U component passes with 50% transmission AND π phase shift (HWP behavior).
- **(2,3) = -C₁:** **Critical term!** Input U leaks into measured Q by fraction C₁.
- **(3,2) = -C₁:** Q leaks into U (reciprocal, assuming reciprocal optics).
- **(4,4) = 1/2:** Circular polarization (V) unaffected.

### 1.7 U-arm Mueller Matrix (HWP at 22.5°)

The U-arm uses a second fixed HWP oriented at 22.5° instead of 0°.

**Why 22.5°?** 

An HWP at 0° analyzes Q polarization (as we just described). An HWP at 22.5° is rotated by 45°, which makes it analyze the U polarization instead. This is because:

```
HWP rotation by angle φ: M_HWP(φ) rotates the analysis direction by 2φ

At φ = 0°: analyzes Q
At φ = 22.5°: analyzes Q rotated by 2×22.5° = 45° → analyzes U
At φ = 45°: analyzes Q rotated by 90° → analyzes -U (opposite direction)
```

**Ideal Mueller for HWP at 22.5°:**

```
M_HWP2_ideal(22.5°) = [1,  0,          0,            0]
                      [0,  cos(90°),  sin(90°),     0]
                      [0, -sin(90°),  cos(90°),     0]
                      [0,  0,          0,            1]

Substituting cos(90°)=0, sin(90°)=1:

M_HWP2_ideal(22.5°) = [1,  0,  1,  0]
                      [0, -1,  0,  0]
                      [0,  0,  0,  0]
                      [0,  0,  0,  1]
```

**Wait, this doesn't look right. Let me reconsider:**

Actually, the Mueller matrix for an HWP at angle φ in the standard formalism is:

```
M_HWP(φ) = [1,           0,            0,                        0           ]
           [0,  cos(4φ),      sin(4φ),      0           ]
           [0, -sin(4φ),      cos(4φ),      0           ]
           [0,           0,            0,                        1           ]
```

At φ = 0°: M_HWP(0°) has (cos(0°), sin(0°), -sin(0°), cos(0°)) = (1, 0, 0, 1) → pure Q analyzer ✓

At φ = 22.5°: 4φ = 90°, so M_HWP(22.5°) has (cos(90°), sin(90°), -sin(90°), cos(90°)) = (0, 1, -1, 0)

```
M_HWP2_ideal(22.5°) = [1,  0,   0,  0]
                      [0,  0,   1,  0]
                      [0, -1,   0,  0]
                      [0,  0,   0,  1]
```

**Physical interpretation:**
- Row 2, Column 2: (Q component of output) from (Q component of input) = 0 (decoupled!)
- Row 2, Column 3: (Q component of output) from (U component of input) = 1 (U→Q leakage, pure!)
- Row 3, Column 2: (U component of output) from (Q component of input) = -1 (Q→U flip)
- Row 3, Column 3: (U component of output) from (U component of input) = 0 (decoupled!)

So the U-arm at 22.5° measures U polarization. The ordinary beam from the WP now gets the U component, and the extraordinary beam gets a flipped Q.

**Real Mueller (with oblique incidence, stress, etc.):**

```
M_HWP2_real(x,y,T) ≈ [1,                                    0,      0,      0    ]
                      [0,  -C_HWP2(x,y,T),   1-C_HWP2(x,y,T), 0    ]
                      [0,  -(1-C_HWP2(x,y,T)),  -C_HWP2(x,y,T),  0    ]
                      [0,                                    0,      0,      1    ]
```

Where C_HWP2(x,y,T) has the same functional form as C_HWP1, but with potentially different coefficients (different mounting, stress patterns, etc.).

**Combined U-arm Mueller:**

```
M_total^(2)(x,y,T) ≈ [1/2,    0,                    0,              0    ]
                     [0,     -1/2,                  (1/2)(1-C₂),    0    ]
                     [0,     -(1/2)(1-C₂),        -1/2 C₂,         0    ]
                     [0,      0,                    0,              1/2  ]
```

---

## SECTION 2: FOURIER DECOMPOSITION PRINCIPLE

### 2.1 Why Fourier? The Modulation Problem

During calibration, the **internal rotating HWP** (different from the two fixed HWPs) is stepped through 16 angles:

```
α_i = i × 22.5°,  i = 0, 1, 2, ..., 15
```

This rotating HWP adds an additional Mueller transformation on top of the fixed HWP:

```
M_rotating(α) = [1,           0,            0,                  0      ]
                [0,  cos(4α),      sin(4α),      0      ]
                [0, -sin(4α),      cos(4α),      0      ]
                [0,           0,            0,                  1      ]
```

The rotating HWP's angle-dependent Mueller matrix means the measured signal **oscillates sinusoidally** as we step through the 16 angles.

**For a source with constant input polarization S_sky (determined by CLP angle θ):**

```
Measured signal at angle i: R_i = f(α_i, θ) 
                              = some function involving cos(4α_i) and sin(4α_i) terms
```

This function is **periodic with period 90°** (since 4α repeats every π radians).

Over our 16 measurements spanning 360°, the signal has a specific periodic structure. We want to decompose this structure into its **frequency components**.

### 2.2 Mathematical Formulation

From Mueller matrix theory (de Bernardis equations 37-40, extended to rotating HWP):

The measured intensity ratio is:

```
R(α, θ) = a₀(θ)  +  [a₂(θ) cos(2α) + b₂(θ) sin(2α)]
                  +  [a₄(θ) cos(4α) + b₄(θ) sin(4α)]
                  +  [a₆(θ) cos(6α) + b₆(θ) sin(6α)]
                  +  ...
                  +  [a₁(θ) cos(α) + b₁(θ) sin(α)]    [mechanical wobble]
                  +  [a₃(θ) cos(3α) + b₃(θ) sin(3α)]  [3-fold asymmetry]
```

**Where:**
- **a₀:** DC offset (detector imbalance, same for all angles)
- **a₂, b₂:** Rotating HWP retardance error (2f modulation from Mueller coupling)
- **a₄, b₄:** True polarization signal + fixed HWP contamination (4f modulation, this is what we want!)
- **a₆, b₆, ... (even):** Higher harmonics from nonlinearity
- **a₁, b₁:** Mechanical wobble (should be ~zero for a perfect instrument)
- **a₃, b₃:** 3-fold asymmetry from bearing defects (should be ~zero)

**The crucial insight:** Each harmonic n has a different **physical origin**. They are naturally separated in frequency space (Fourier components). By extracting each harmonic independently, we isolate each physical effect.

### 2.3 Discrete Fourier Transform (DFT) Extraction

With 16 evenly-spaced samples over 360° (Δα = 22.5°), we can perform a standard DFT:

```
For each harmonic n = 0, 1, 2, 3, 4, ...:

a_n(θ) = (1/16) × Σ_{i=0}^{15} R_i(θ) × cos(n × α_i)

b_n(θ) = (1/16) × Σ_{i=0}^{15} R_i(θ) × sin(n × α_i)

A_n(θ) = √(a_n² + b_n²)    [magnitude, phase-invariant]

φ_n(θ) = atan2(b_n, a_n)   [phase, orientation of error]
```

**Why 16 samples?** Nyquist-Shannon theorem: To resolve frequency f_max without aliasing, need at least 2f_max samples. Here, f_max = 4 (the 4f polarization modulation), so we need ≥ 8 samples. We use 16 for safety margin and better noise averaging.

### 2.4 Orthogonality Property (Why No Cross-talk Between Harmonics)

A fundamental property of sinusoidal functions:

```
(1/N) × Σ_{i=0}^{N-1} cos(n × α_i) × cos(m × α_i) = { 1/2 if n = m
                                                         0   if n ≠ m

(1/N) × Σ_{i=0}^{N-1} sin(n × α_i) × sin(m × α_i) = { 1/2 if n = m
                                                         0   if n ≠ m

(1/N) × Σ_{i=0}^{N-1} cos(n × α_i) × sin(m × α_i) = 0   for all n,m
```

**Physical consequence:** When we extract a_4 (the 4f component), the n=2 terms in R(α) contribute **exactly zero** to a_4 because of orthogonality. The n=4 and n=2 harmonics are mathematically independent.

**Why this matters:**
- In a non-ideal instrument, n=2 and n=4 are NOT physically independent (they both depend on optical elements).
- But mathematically, the Fourier extraction **automatically decouples them**.
- This is why Fourier is so powerful: it gives us a clean separation even when the underlying physics is coupled.

### 2.5 Physical Meaning of Each Harmonic

**n=0 (DC component):**

```
a₀ = (1/16) × Σ_i R_i  = arithmetic mean of all 16 measurements

Physical meaning:
- The detector's baseline offset (dark current, bias, electronic zero-point)
- The imbalance between ordinary and extraordinary beam transmission through the arm
- Instrumental linear polarization (if the instrument itself is slightly polarized)

Example:
- Ideal unpolarized light (p=0) should give R=0
- Real measurement of unpolarized light gives R ≈ a₀ ≠ 0
- This offset must be subtracted from subsequent Stokes parameter measurements
```

**n=2 (2f modulation, rotating HWP error):**

```
A₂ = √(a₂² + b₂²)  [magnitude]
φ₂ = atan2(b₂, a₂) [phase]

Physical origin:
- The rotating calibration HWP is not perfect
- Designed retardance: δ = π (exactly 90°)
- Actual retardance: δ = π + ε,  where |ε| ~ 0.01 to 0.05 rad (0.5° to 3°)
- This extra phase ε creates a 2f modulation (from Mueller theory equation 37-40 in de Bernardis)
- Magnitude A₂ directly indicates "how bad" the rotating HWP error is
- Phase φ₂ indicates the orientation (which axis is misaligned)

Mueller coupling mechanism:
The rotating HWP error ε mixes into the measurement as:
R(α) ≈ ... + [ε × sin²(α) or ε × cos²(α)] + ...
These sin²(α) and cos²(α) terms can be rewritten as (1 - cos(2α))/2 type expansions
Which gives rise to 2f (cos(2α), sin(2α)) harmonic components
```

**n=4 (4f modulation, true signal + fixed HWP cross-talk):**

```
a₄(θ), b₄(θ)  [or amplitude A₄ and phase φ₄]

Physical origin:
- TRUE polarization signal from the star
  (Q component for Q-arm, U component for U-arm)
- CONTAMINATED by fixed HWP cross-talk
  (Q→U leakage in U-arm, U→Q leakage in Q-arm)

For Q-arm measuring pure Q (θ=0°):
a₄^(1) ≈ Q_true / 2   (factor of 1/2 from beam splitter)
         - C₁ × U_true / 2  (leakage from U)
         + Mueller coupling × A₂²  (contamination from rotating HWP error)

So the "pure" n=4 signal is mixed with several contaminants that must be removed.

Why 4f?
The rotating HWP has cos(4α) and sin(4α) terms in its Mueller matrix (retardance π means 4 cycles per full rotation).
The true Q/U polarization is analyzed by this 4f analyzer.
Hence the signal appears at 4f frequency.
```

**n=1, n=3 (Mechanical wobble/asymmetry):**

```
A₁ = √(a₁² + b₁²)
A₃ = √(a₃² + b₃²)

Physical origin:
- n=1: Mechanical wobble (rotating HWP not centered on axis, bearing play, etc.)
- n=3: 3-fold asymmetry (bearing defect, mount clamp asymmetry)

Why they shouldn't exist:
- The HWP has 2-fold symmetry (it repeats every 180°)
- A perfect 2-fold system cannot produce 1-fold or 3-fold harmonics
- Appearance of A₁, A₃ indicates INSTRUMENT DEGRADATION

Typical values:
- Perfect instrument: A₁, A₃ < 0.001 (essentially zero)
- Worn bearing: A₁ ~ 0.01 to 0.1 (measurable)
- Severely degraded: A₁ > 0.1 (obvious problem)

Use as diagnostic:
- Monitor A₁(t), A₃(t) over months of observations
- When they exceed 0.01, schedule bearing replacement
- This is CONDITION-BASED MAINTENANCE signal
```

---

## SECTION 3: ONE-SHOT EQUIVALENT RECONSTRUCTION

### 3.1 The Conceptual Leap

WALOP as a **science instrument** operates in "one-shot" mode:

```
SCIENCE MODE:
- Single exposure (no rotation)
- Fixed HWP at 0° or 22.5°
- 4 detectors measure simultaneously: I_o1, I_e1, I_o2, I_e2
- Compute: Q_measured = (I_o1 - I_e1)/(I_o1 + I_e1)
           U_measured = (I_o2 - I_e2)/(I_o2 + I_e2)
- This is what we get on sky
```

But for **calibration**, we add the rotating HWP to modulate the signal:

```
CALIBRATION MODE:
- 16 exposures per CLP angle (due to rotating HWP)
- Same fixed HWPs (0° and 22.5°)
- Rotating HWP creates 4f modulation
- Extract 4 Fourier coefficients per arm: a₀, a₂, b₂, a₄, b₄
```

**The problem:** Our calibration data (16 angles) is different from science data (1 angle). How do we make them comparable?

**The solution:** Use Fourier extraction to "remove" the rotating HWP, reconstructing what the one-shot measurement would have been.

### 3.2 Mueller Matrix Theory of Contamination

From equation 37-40 in de Bernardis paper, the Mueller coupling term that contaminates n=4 due to rotating HWP error ε is:

```
Contamination_ε ∝ ε × [Mueller projection factor]
                  ∝ A₂  [since A₂ ∝ |ε|]
```

More explicitly (de Bernardis eq. 47):

```
p₁ ≈ p₀ + (ε/4) × (d - a)/a × cos(2ψ)

where:
p₁ = first-order polarization degree estimate with retardance error
p₀ = zeroth-order (ideal) estimate
ε = retardance error (in radians)
(d-a)/a = measured Fourier ratio
ψ = source polarization angle

The bias term ∝ (ε) × (measured ratio) ∝ A₂ × (Fourier coefficient)
```

This justifies the correction formula:

```
Q_m = a₄^(1) - K₁ × A₂^(1)
U_m = a₄^(2) - K₂ × A₂^(2)
```

Where:
- **Q_m, U_m** = "measured" quantities as if we had one-shot mode (rotating HWP artifact removed)
- **a₄^(k)** = extracted Fourier coefficient from calibration data
- **A₂^(k)** = magnitude of rotating HWP error (also from Fourier)
- **K₁, K₂** = coupling coefficients (empirically determined from calibration fit)

### 3.3 Computing A₂ Magnitude

During the Fourier extraction, we get:

```
a₂^(k) = (1/16) × Σ_{i=0}^{15} R_i^(k) × cos(2α_i)
b₂^(k) = (1/16) × Σ_{i=0}^{15} R_i^(k) × sin(2α_i)

A₂^(k) = √(a₂^(k)² + b₂^(k)²)
```

**Why magnitude and not just a₂?**

The 2f contamination has both cosine and sine components. The cosine component a₂ depends on the phase of the HWP fast axis (φ_error). If φ_error = 0°, then a₂ captures everything. If φ_error = 90°, then b₂ captures everything and a₂ ≈ 0.

But the physical error (bad HWP retardance) exists regardless of its orientation. The **total magnitude** A₂ = √(a₂² + b₂²) is phase-invariant and represents the true "badness" of the rotating HWP.

By using A₂ in the correction, we account for the error no matter how it's oriented.

### 3.4 Example Calculation

**Scenario:** During calibration, we measure the Q-arm with a pure Q input (θ=0°).

**Step 1: Fourier extraction at one grid point, one CLP angle:**

```
16 measurements: R_i, i = 0 to 15

Typical measured values (example):
i:    0      1      2      3      4      5      ...     15
R_i:  0.502  0.501  0.485  0.460  0.450  0.460  ...     0.501
```

(R oscillates between ~0.50 and ~0.45, with 4 full oscillations per 360°)

**Step 2: Extract Fourier components:**

```
a₀ = mean ≈ 0.498  [small offset]
a₂ = weighted sum of cos(2α_i) ≈ 0.010
b₂ = weighted sum of sin(2α_i) ≈ 0.012
a₄ = weighted sum of cos(4α_i) ≈ 0.500
b₄ = weighted sum of sin(4α_i) ≈ 0.001

A₂ = √(0.010² + 0.012²) ≈ 0.016
```

**Step 3: Apply correction:**

```
Assuming K₁ ≈ 0.05 (empirical value):

Q_m = a₄ - K₁ × A₂
    = 0.500 - 0.05 × 0.016
    = 0.500 - 0.0008
    = 0.4992
```

This Q_m ≈ 0.499 is now the "one-shot equivalent" with rotating HWP error removed.

---

## SECTION 4: FIXED HWP CROSS-TALK EXTRACTION (C₁, C₂)

### 4.1 The Pure-State Measurement Protocol

To extract C₁ and C₂, we use **calibration sources with known polarization states** (the CLP at specific angles θ).

```
CLP angle θ = 0°:   Pure Q state
               S = [1, 1, 0, 0]^T  (perfectly polarized along Q)

CLP angle θ = 90°:  Pure U state
               S = [1, 0, 1, 0]^T  (perfectly polarized along U)
```

**Measurement protocol:**

For each CLP angle, we:
1. Rotate the internal HWP through 16 angles (same as before)
2. Extract Fourier components per arm
3. Compute the one-shot equivalents Q_m, U_m using the A₂ correction
4. Compare measured Q_m, U_m against expected value

### 4.2 Mathematical Extraction

**When CLP is at θ=0° (pure Q):**

The INPUT Stokes vector after the BK7 wedge is:

```
S_sky = [I_0/2, I_0/2, 0, 0]^T    (pure Q, after beam split)
```

Expected output per arm (IDEAL optics):

```
Arm 1 (Q-arm):  Q_m^(1) = I_0/4   (pure Q signal)
                U_m^(1) = 0        (should be zero)

Arm 2 (U-arm):  Q_m^(2) = 0        (should be zero)
                U_m^(2) = 0        (should be zero, U-arm doesn't measure Q ideally)
```

**REAL measurement (with cross-talk):**

```
Arm 1 (Q-arm):  Q_m^(1) ≈ I_0/4 × (1 - C₁)   [slightly reduced]
                U_m^(1) ≈ 0                    [ideally zero]

Arm 2 (U-arm):  Q_m^(2) ≈ 0                   [ideally zero]
                U_m^(2) ≈ I_0/4 × C₂           [LEAKAGE! Should be zero]
```

**Define:**
```
C₂(x,y) = [U_m^(2) measured at θ=0°] / [Q_m^(1) measured at θ=0°]
        = [how much Q leaks into U arm] / [true Q signal in Q arm]
```

### 4.3 Mueller Matrix Inversion

The 2×2 effective Mueller matrix per arm can be written as:

```
At each grid point (x,y):

M^(1)_eff = [1,      -C₁(x,y)   ]    [Q-arm effective Mueller]
            [-C₂(x,y),    -1     ]

with determinant: det = -1 - C₁C₂ ≈ -1 (for small C)
```

**Inversion formula (matrix inverse of 2×2):**

```
M⁻¹ = 1/det × [  -1,      C₁   ]
               [ C₂,        1   ]

     ≈  1/(1 + C₁C₂) × [ -1,      C₁   ]
                       [ C₂,       1   ]

     ≈ [-1 + C₁C₂,        C₁(1-C₁C₂)  ]   [to first order in C]
       [ C₂(1-C₁C₂),      1 - C₁C₂     ]
```

**Application (science data):**

When you measure raw [Q_measured, U_measured] and want to recover [Q_true, U_true]:

```
[Q_true  ]   = M⁻¹ × [Q_measured]
[U_true ]            [U_measured]

Q_true ≈ -Q_measured + C₁ × U_measured
U_true ≈ -U_measured + C₂ × Q_measured   (plus small second-order terms)
```

Or in your calibration form:

```
Q_true = α_q × Q_measured + β_q × C₁ × U_measured + ...
U_true = α_u × U_measured + β_u × C₂ × Q_measured + ...
```

---

## SECTION 5: LEAST-SQUARES CALIBRATION FITTING

### 5.1 The Regression Problem

After extracting Q_m, U_m, C₁, C₂, A₀ for all calibration sources, we solve an overdetermined linear system to find the best-fit calibration coefficients.

**For the Q-arm:**

```
Fit model:
q_calib^(j) = α_q × Q_m^(j) + β_q × C₁(x,y) × U_m^(j) + δ_q × A₀^(1)(j) + γ_q × (Q_m^(j))² + ε_q

For j = 1, 2, ..., N sources (N ≥ 10)
```

**Known quantities:**
- q_calib^(j) = expected true Q value for source j (from catalog or CLP angle)
- Q_m^(j) = measured one-shot equivalent from calibration data
- U_m^(j) = measured one-shot equivalent from calibration data
- A₀^(1)(j) = extracted DC offset for that measurement
- C₁(x,y) = fixed HWP cross-talk (estimated from pure-state measurements)

**Unknown parameters to solve:**
- α_q = Q-arm efficiency
- β_q = Q-arm U-leakage coefficient
- δ_q = Q-arm offset sensitivity
- γ_q = Q-arm nonlinearity
- ε_q = residual offset term

**Same for U-arm with parameters (α_u, β_u, δ_u, γ_u, ε_u).**

### 5.2 Matrix Formulation

Stack all sources into matrix form:

```
Measurement vector:
Y^(Q) = [q_calib^(1)  ]
        [q_calib^(2)  ]
        [...          ]
        [q_calib^(N)  ]

Design matrix (N × 5):
X = [Q_m^(1),   C₁U_m^(1),   A₀^(1)(1),   (Q_m^(1))²,   1]
    [Q_m^(2),   C₁U_m^(2),   A₀^(1)(2),   (Q_m^(2))²,   1]
    [...        ...           ...         ...            ...]
    [Q_m^(N),   C₁U_m^(N),   A₀^(1)(N),   (Q_m^(N))²,   1]

Coefficient vector:
θ_Q = [α_q    ]
      [β_q    ]
      [δ_q    ]
      [γ_q    ]
      [ε_q    ]

Linear system:
Y^(Q) = X × θ_Q + residuals
```

### 5.3 Least-Squares Solution

Minimize the residual sum of squares:

```
χ² = Σ_j [q_calib^(j) - (α_q Q_m^(j) + β_q C₁ U_m^(j) + δ_q A₀^(1)(j) + γ_q (Q_m^(j))² + ε_q)]² / σ_j²

where σ_j = uncertainty in q_calib^(j) (photon noise + calibration uncertainty)
```

**Normal equations (setting ∂χ²/∂θ = 0):**

```
(X^T W X) θ = X^T W Y

where W = diagonal weight matrix, W_jj = 1/σ_j²

Solution:
θ = (X^T W X)⁻¹ (X^T W Y)

Uncertainty:
Cov(θ) = (X^T W X)⁻¹
σ(θ_i) = √(Cov(θ)_ii)
```

### 5.4 Validation Steps

After solving for each grid:

1. **Residual check:** Compute fitted residuals for each source, plot histogram. Should be Gaussian with σ ~ photon noise.

2. **Parameter correlation:** Check Cov(θ) matrix off-diagonals. Large correlations (ρ > 0.7) suggest either:
   - Too many parameters (reduce model complexity)
   - Poor source selection (use more diverse CLP angles)

3. **Reduced χ²:** Compute χ²/dof = Σ(residual²) / (N - 5). Should be ≈ 1. If >> 1, fit is poor; if << 1, errors are overestimated.

4. **Outlier detection:** Flag any source with |residual| > 3σ, investigate (bad measurement, incorrect catalog value, etc.).

---

## SECTION 6: SCIENCE-NIGHT CALIBRATION APPLICATION

### 6.1 Measurement Sequence

**Observing a science target at position (x_target, y_target):**

```
Step 1: ACQUISITION
  - Point telescope at target
  - Open shutter
  - Expose for exposure time t_exp
  - Read 4 detector signals: I_o1, I_e1, I_o2, I_e2

Step 2: COMPUTE ONE-SHOT EQUIVALENTS (raw, before calibration)
  Q_raw = (I_o1 - I_e1) / (I_o1 + I_e1)
  U_raw = (I_o2 - I_e2) / (I_o2 + I_e2)

Step 3: LOCATE CALIBRATION GRID
  Find nearest grid point: (x_cal, y_cal)
  Distance to target: Δx_cal = x_target - x_cal, Δy_cal = y_target - y_cal
  
  If Δx, Δy < half grid spacing (allowed):
    Option A: Use nearest grid directly
    Option B: Bilinear interpolate from 4 nearest grids
    
  If distance exceeds 1 grid spacing:
    WARNING: target too far from calibration grid, interpolation invalid

Step 4: LOAD CALIBRATION COEFFICIENTS
  From calibration database, retrieve for grid (x_cal, y_cal):
  
  {α_q, β_q, δ_q, γ_q, ε_q,
   α_u, β_u, δ_u, γ_u, ε_u,
   C₁, C₂, A₀^(1), A₀^(2)}

Step 5: APPLY CALIBRATION TRANSFORMATION
  
  Q_calib = α_q × Q_raw + β_q × C₁ × U_raw + δ_q × A₀^(1) + γ_q × Q_raw² + ε_q
  U_calib = α_u × U_raw + β_u × C₂ × Q_raw + δ_u × A₀^(2) + γ_u × U_raw² + ε_u

Step 6: COMPUTE POLARIMETRY
  p = √(Q_calib² + U_calib²) / I_total
  θ_EVPA = (1/2) × atan2(U_calib, Q_calib)

Step 7: ERROR PROPAGATION
  σ_p = √[(∂p/∂Q_calib)² σ_Q² + (∂p/∂U_calib)² σ_U²]
       = √[(Q_calib/p)² σ_Q² + (U_calib/p)² σ_U²]
       
  σ_θ = √[(∂θ/∂Q_calib)² σ_Q² + (∂θ/∂U_calib)² σ_U²]
       = √[(U_calib/p²)² σ_Q² + (Q_calib/p²)² σ_U²] / 2
```

### 6.2 Output Science Product

**For each observed target:**

```
Catalog entry:
  Target name: [identifier]
  RA, Dec: [coordinates]
  Magnitude: [apparent magnitude]
  
  Measured polarimetry:
    p = [Q_calib, U_calib] / I_total  ± σ_p  [polarization degree]
    θ = atan2(U_calib, Q_calib) / 2   ± σ_θ  [position angle in degrees]
    
  Quality flags:
    SNR = I_total / √I_total  (photon SNR)
    GRID_DIST = √[(Δx_cal)² + (Δy_cal)²]  (distance from calibration grid)
    
  Metadata:
    Observation time: [UTC]
    Exposure time: [seconds]
    Air mass: [airmass at observation]
    Temperature: [HWP temperature during obs]
    
 Calibration grid used: (x_cal, y_cal)
    Interpolation order: [nearest / bilinear]
```

---

## SECTION 7: ERROR BUDGET AND UNCERTAINTY ANALYSIS

### 7.1 Sources of Uncertainty

**Photon noise (statistical):**

```
σ_I = √I  [for Poisson statistics]

σ_Q = σ[(I_o - I_e)/(I_o + I_e)]
    ≈ 2√(I_o + I_e) / (I_o + I_e)²
    ≈ 2 / √I_total

For I_total ~ 10,000 counts (typical bright star):
σ_Q ~ 2% (relative)

For I_total ~ 100 counts (faint star):
σ_Q ~ 20% (relative)
```

**Calibration coefficient uncertainty (systematic):**

```
If coefficient α_q determined with uncertainty σ(α_q) ~ 1% from fit:
Systematic bias ≈ σ(α_q) × Q_measured ≈ 0.01 × Q_measured

For weakly polarized star (Q ~ 0.01):
Systematic bias ~ 10⁻⁴  (negligible compared to photon noise)

For strongly polarized standard (Q ~ 0.5):
Systematic bias ~ 0.005  (competitive with photon noise for bright sources)

This is why we need high SNR in calibration!
```

**Temperature drift during night:**

```
HWP birefringence dΔn/dT ≈ -10⁻⁵ K⁻¹
Retardance change: dδ/dT = (2π/λ) × (dΔn/dT) × t ≈ -0.0001 rad/K

Effect on C₁, C₂: dC/dT ≈ 0.001 to 0.005 per Kelvin

Over 10K temperature change (night cooling):
ΔC ≈ 0.01 to 0.05  (potentially significant!)

Mitigation:
- Monitor temperature continuously
- Recalibrate every 2-3 hours if dT > 2K
- Apply temperature correction: C(T) = C(T_ref) + (dC/dT) × (T - T_ref)
```

**Grid interpolation error:**

```
If true C(x,y) has sharp features (e.g., from stress birefringence) with scale ~ 1 grid spacing
Interpolation to halfway between grids introduces error ~ (d²C/dx²) × (grid_spacing)²

Typical: grid_spacing ~ 3' (arcmin), d²C/dx² ~ 0.01/(arcmin)²
Error ~ 0.01 × 9 ~ 0.09 = 9% per interpolation step

Mitigation:
- Use finer grid (12×12 → 16×16 or 20×20) if sharp features present
- Use cubic spline interpolation instead of bilinear
- Flag targets > half grid spacing from nearest calibration point
```

### 7.2 Total Error Budget Example (Bright Star, p ~ 0.05)

```
Component                    Magnitude    Relative Error
─────────────────────────────────────────────────────────
Photon noise (Q)             0.002        4%
Photon noise (U)             0.002        4%
Calibration coefficient      0.0005       1%
Temperature drift            0.001        2%
Grid interpolation           0.0015       3%
Residual systematics         0.0005       1%
─────────────────────────────────────────────────────────
TOTAL (Q or U)               0.0035       7%

Polarization degree: p = √(Q² + U²)
σ_p ~ √[(Q dσ_Q/dp)² + (U dσ_U/dp)²]
    ~ √[(0.05 × 4%)² + (0.05 × 4%)²]
    ~ √[2 × (0.002)²]
    ~ 0.003
    ~ 6% relative, or 0.003 absolute (3 parts per mille)

Target: achieve 0.5% systematic, limit photon noise to < 1% by selecting stars mag < 12
```

---

## SECTION 8: QUALITY ASSURANCE & VALIDATION

### 8.1 Calibration Night Validation

**After fitting 10-15 standard stars per grid:**

1. **Residual analysis:**
   ```
   Plot histogram of fit residuals for each grid
   Check: residuals are Gaussian, mean ≈ 0, σ ≈ photon noise
   Red flag: any grid with σ_residual > 2 × σ_photon (bad fit, outliers)
   ```

2. **Consistency checks across grids:**
   ```
   For each parameter (e.g., α_q), plot its spatial map across 12×12 grid
   Check: smooth variation (no discontinuities)
   Expected: α_q ~ 0.9-1.0 everywhere (arm efficiency should be ~constant)
   Check: β_q ≈ α_q (detector gains matched?)
   ```

3. **Temperature correlation:**
   ```
   Plot C₁(t), C₂(t) vs temperature monitor during night
   If dC/dT > 0.001 K⁻¹: flag for temperature correction in science
   If dC/dT < 0.0001 K⁻¹: temperature stable, no correction needed
   ```

4. **Mechanical diagnostics:**
   ```
   Monitor A₁, A₃ harmonics (wobble, asymmetry)
   If A₁ > 0.01: bearing needs inspection
   If A₁ increasing with time: predict maintenance date
   ```

### 8.2 Science Data Validation

**For each observed target:**

1. **SNR check:**
   ```
   Compute SNR = √I_total
   
   SNR > 100: high confidence (photon noise < 1%)
   SNR 30-100: moderate confidence (photon noise 1-3%)
   SNR < 30: low confidence, flag as tentative
   ```

2. **Outlier detection:**
   ```
   For standard stars observed multiple times:
   Compare p, θ across different observing nights
   Scatter should be < 1% (photon + small systematics)
   If scatter > 5%: investigate (weather effect? calibration change?)
   ```

3. **Cross-calibration:**
   ```
   Compare WALOP polarimetry against literature values for known standards
   Residuals should follow photon noise distribution
   Any systematic bias > 0.5% suggests calibration error
   ```

---

## CONCLUSION: FROM CALIBRATION TO SCIENCE

Your calibration model provides a **physics-transparent pathway** from raw detector signals to calibrated polarimetry:

```
Raw intensities  →  Fourier extraction  →  One-shot equivalents  →  Least-squares fit  →  Calibration database
(I_o, I_e)       (a₀, a₂, A₂, a₄)    (Q_m, U_m, C₁, C₂)       (α_q, β_q, δ_q, γ_q)  {144 grids, 10 params each}

                                                                                            ↓
                                                                                    Science observation
                                                                                    (one exposure, raw Q_raw, U_raw)
                                                                                    ↓
                                                                                    Interpolate to nearest grid
                                                                                    ↓
                                                                                    Apply: Q_calib = α_q Q_raw + β_q C₁ U_raw + ...
                                                                                    ↓
                                                                                    True Q, U, p, θ
```

Each step has explicit Mueller matrix justification. Each parameter has physical meaning. The model is tested rigorously on calibration data before deployment.



