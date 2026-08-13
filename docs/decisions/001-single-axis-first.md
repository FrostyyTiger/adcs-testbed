# 001: Single-axis first, designed for 3-axis expansion

**Date:** 2026-08-11
**Status:** accepted; **reframed 2026-08-13 by [ADR 005](005-coupled-manipulator-scope.md)**

> **Reframing note.** Single-axis is no longer a stepping stone toward a three-axis cube —
> it is the permanent base configuration of the coupled manipulator testbed, and it is now
> *required* rather than merely convenient: a 3-DOF arm on a three-axis base has an empty
> reaction null space. The reasoning below still stands; only the destination changed.
> Three-axis operation moved to phase 3.

## Context

The end goal is a 3-axis reaction-wheel ADCS demonstrator, but the project has a 3–4 month
timeline and the hardest technical risks (smooth wheel torque control through zero speed,
attitude estimation, stand friction) are independent of axis count. Debugging all of them
simultaneously in 3 DOF multiplies schedule risk.

## Options considered

1. **Single-axis (yaw) first, expand later** — one wheel, cube on a low-friction rotary
   stand. All core capabilities (point-and-hold, disturbance rejection, detumble, target
   tracking) demonstrable in 1 DOF.
2. **3-axis from day one** — three wheels, spherical air bearing or string suspension.
3. **Single-axis only** — polished 1-DOF demonstrator, never expanded.

## Decision

Option 1. The cube structure carries mounting points for three orthogonal wheel assemblies
from the first CAD revision; the firmware treats wheel torque commands as a vector from the
start (with two entries zeroed); the power budget assumes three motors.

## Consequences

- Phase 1 hardware spend stays ~250–350 CHF; the remaining budget covers expansion.
- Yaw-axis control on a bearing stand is demonstrable within weeks, giving early portfolio
  material (video of point-and-hold, detumble).
- 3-axis expansion needs only two more motor/driver/encoder sets and a stand upgrade
  (string suspension or spherical bearing) — no redesign, if the interfaces are respected.
- Attitude estimation can mature from a 1-DOF complementary filter to a full quaternion
  EKF against the same firmware interfaces.
