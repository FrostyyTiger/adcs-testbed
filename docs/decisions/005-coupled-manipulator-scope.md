# 005: Scope — coupled attitude and manipulator testbed, yaw-only base

**Date:** 2026-08-13
**Status:** accepted — reframes ADR 001

## Context

The project began as a 1U reaction-wheel attitude control demonstrator (ADR 001–004).
That is a crowded genre. The coupled base–manipulator control problem — how a free-floating
spacecraft holds attitude while its own arm moves — is a much less crowded one, is
directly relevant to on-orbit servicing and debris capture (ETS-VII, MEV-1/2, ESA ADRIOS
with ClearSpace), and turns the build into an experiment with a measurable result rather
than a demonstration.

The intended study compares two established approaches on identical hardware:

- **Feedforward momentum compensation** — the wheel absorbs the arm's predicted reaction
  torque; the arm path is unconstrained.
- **Reaction null-space (RNS) planning** — joint trajectories are constrained to transfer
  zero angular momentum to the base; the wheel handles only residual model error.

Against an uncompensated baseline (arm slews, wheel idle).

## The constraint that sets the configuration

For a free-floating base with zero initial angular momentum, joint rates couple to base
motion through the coupling inertia matrix H_bm:

    H_b · ω_b + H_bm · q̇ = 0

Zero-reaction motion requires q̇ ∈ null(H_bm). The null space has dimension
n − rank(H_bm), where n is the arm's degrees of freedom:

| Base | H_bm | Null space dimension, n = 3 |
| --- | --- | --- |
| 3-axis | 3 × n | **0** — no zero-reaction motion exists |
| Yaw-only | 1 × n | **2** |

A 3-DOF arm on a 3-axis base therefore leaves RNS planning with nothing to work with;
a non-trivial spatial null space needs n ≥ 4, and realistically 5–6 for useful
trajectories. On a yaw-only base, a 3-DOF planar arm is exactly the minimum configuration
that can trace arbitrary zero-reaction paths in (x, y) — two task constraints plus one
zero-reaction constraint against three joints. Commanding end-effector *orientation*
simultaneously would require a fourth joint.

## Decision

1. **Base is yaw-only**: the 1U cube rotates about a single vertical axis on a low-friction
   bearing, with one reaction wheel. Three-axis operation is deferred to phase 3.
2. **Arm is a 3-DOF planar manipulator** mounted on the base, moving in the horizontal
   plane (so joints never work against gravity).
3. **No spherical air bearing.** Commercial units are far outside the budget and DIY
   versions need tolerances a hobbyist FDM printer will not hold, plus a compressed-air
   supply. The rotational free-floating approximation is achieved with the bearing stand.
   The upgrade path, if translation is ever wanted, is a *planar* air bearing (float pucks
   on a flat plate, as used by university free-flyer testbeds), not a spherical one.
4. **Wheel is sized by arm reaction momentum**, not by base slew rate — see Consequences.

## Consequences

**Honest limitation to state in any writeup:** with a fixed pivot, the arm's *linear*
reaction is absorbed by the bearing rather than translating the base. This is a
rotational-coupling testbed, not a full free-flyer. The rotational coupling physics that
both controllers address is preserved intact.

**Wheel sizing changes.** Taking a shell-like 1U base at I_b ≈ 1.5×10⁻³ kg·m², a printed
arm has inertia *comparable to the entire base*:

| Arm | I_arm / I_base | Base swing, 90° slew, wheel idle |
| --- | --- | --- |
| 100 g, 12 cm reach | 0.32 | 22° |
| 200 g, 15 cm reach | 1.00 | 45° |
| 300 g, 20 cm reach | 2.67 | 66° |

A 200 g arm slewing 90° in 0.5 s demands about 122% of the originally-sized wheel's
momentum capacity — saturation mid-manoeuvre. Therefore: arm moving mass held to ≤ ~150 g,
reach ~12–15 cm, slews budgeted at 1–2 s, and wheel inertia raised to ≥ 3×10⁻⁵ kg·m²
(≈2.5× the ADR 003 sizing, achieved with rim radius and steel dowel ballast).

This also makes **saturation margin a headline metric rather than a footnote**, and it is
the honest reason RNS planning is interesting: it consumes no wheel momentum at all.

**Schedule.** Roughly 5–7 months rather than 3–4: base attitude control 6–8 weeks, arm and
coupled study another 8–10. Phase 1 remains a complete, presentable project on its own.

**Cost.** Approximately neutral. Two of three reaction-wheel motor/driver/encoder sets are
deferred, funding the arm; the air bearing that would have broken the budget is out of
scope. See [BOM](../bom.md).

**Simulation becomes more valuable.** The entire planner comparison can be produced in
simulation before any arm hardware exists, which de-risks the build and yields early
results.
