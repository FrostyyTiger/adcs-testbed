# 007: Manufacturing — FDM printing as project instrumentation, not just fabrication

**Date:** 2026-08-13
**Status:** accepted

## Context

The structure, reaction wheel, arm links, stand, and fixtures are all fabricated in-house
on a Bambu Lab P-series printer with an AMS multi-material unit. The obvious framing is
"3D printing is how the parts get made." That undersells what it enables for an experiment
of this kind, and the design should be organised around the difference.

## Decision

Print essentially all mechanical parts, and deliberately exploit three properties of
in-house printing that a bought-parts build does not have.

### 1. Fixed parameters become swept experimental variables

- **Arm inertia.** Two or three interchangeable link sets with deliberately different mass
  distributions (hollow vs. ballasted, short vs. long reach) let coupling severity become
  an *independent variable*. "How does the relative advantage of null-space planning over
  feedforward compensation change as arm-to-base inertia ratio goes from 0.3 to 2.7?" is a
  result, not a build step.
- **Wheel variants.** Rim radius and ballast mass are the two knobs on momentum capacity.
  Several discs can be printed in a day, and the capacity-versus-vibration trade measured
  rather than argued.

### 2. Mass properties are known a priori

Every structural part is designed in CAD with known material density and infill, so the
link inertias entering the coupling matrix H_bm are computed rather than estimated — then
validated against measurement with a printed trifilar-pendulum rig and by weighing each
part as it comes off the plate. The plant model can be stated with confidence, which is
precisely what the control results rest on.

### 3. Test fixtures are nearly free

Printed measurement and calibration hardware is what makes the results quantitative:

- **Trifilar pendulum rig** — measures moment of inertia of the base and each link set;
  validates CAD mass properties.
- **Encoder magnet concentricity jig** — the diametric magnet must sit centred on the
  motor shaft with a controlled air gap; a jig makes this repeatable rather than a gluing
  gamble.
- **IMU calibration cube** — precise orthogonal faces for 6-position accelerometer and
  magnetometer calibration.
- **Repeatable disturbance injector** — a pendulum released from a fixed detent delivers a
  known angular impulse to the base, replacing "poke it and see" with a reproducible,
  quantitative disturbance-rejection test.

### Multi-material (AMS) uses that are structural, not cosmetic

- **TPU-isolated IMU mount printed as a single part** — rigid shell with flexible grommets
  where it bolts down. Reaction wheel and servo vibration coupling into the IMU is a real
  corruption path for the attitude estimate; this is the clean fix.
- **TPU strain reliefs and service loops** so arm wiring adds neither stiffness nor an
  unmodelled disturbance torque.
- **Dissimilar-material breakaway supports** (PLA releases cleanly from PETG and vice
  versa) for internal channels in links and brackets.
- **Axis colour coding printed into the structure** (X red, Y green, Z blue). Cosmetic in
  isolation; in a documentation-heavy open-source project it makes every photograph,
  video frame, and diagram immediately legible.

## Materials

| Part | Material | Reason |
| --- | --- | --- |
| Structure, panels, trays | PETG (ASA if the chamber allows) | Tough, dimensionally stable, tolerant near warm motors |
| Arm links | PLA-CF or PETG-CF | Stiffness dominates — flex breaks the momentum model both controllers depend on. Requires a hardened nozzle |
| Reaction wheel | PETG + steel dowel ballast | Discrete masses at max radius; individually adjustable for balance |
| Isolation mounts, strain reliefs | TPU | Multi-material with the rigid shell |
| Supports | PVA or dissimilar material interface | Clean internal geometry |

## Consequences

- **Wheel balance is the principal manufacturing risk.** A printed disc at 3000–6000 rpm
  with a few grams of asymmetry will shake the testbed and pollute the IMU. Mitigation:
  static-balance on a knife edge, then use the IMU itself as the balance instrument —
  spin up, log accelerometer magnitude against wheel speed, add trim mass, repeat. The
  procedure produces a figure for the writeup.
- Layer adhesion is the weak axis: parts are oriented so wheel hoop stress and joint loads
  act in-plane.
- Press fits (bearing seats, dowel pockets) should be expected to take two iterations.
- A hardened nozzle is a prerequisite for any CF- or metal-filled filament.
- Every printed part is weighed and recorded — free data that validates the CAD mass
  properties the controller depends on.
