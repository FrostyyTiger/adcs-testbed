# Simulation

Python model of the coupled base–manipulator dynamics, used for control design, planner
development, and tuning before anything runs on hardware. Phase 0 of the
[roadmap](../docs/roadmap.md) lives here, and the full planner comparison can be produced
in simulation before the arm exists.

## Model

- **Base** (yaw only): `I_b·θ̈_b = −τ_w + τ_dist`
- **Reaction wheel:** `I_w·ω̇_w = τ_motor − τ_friction`
- **Coupling:** angular momentum about the pivot is conserved; the arm contributes
  `H_bm(q)·q̇`, where `H_bm` is the configuration-dependent coupling inertia built from
  link mass properties.

## Planned contents

- Plant model with sensor noise, quantisation, and joint tracking error
- Construction of `H_bm(q)` from CAD-derived link inertias
- Three control cases: uncompensated baseline, feedforward momentum compensation
  (`h_w = −H_bm(q)·q̇`), reaction null-space planning (task velocity projected into
  `null(H_bm)`)
- PID tuning for base attitude hold, and detumble (rate damping)
- Metric extraction: pointing error, momentum accumulation, saturation margin
- Attitude estimation prototyping: complementary filter → MEKF
