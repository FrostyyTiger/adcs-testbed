# 006: Arm actuators — smart servos with velocity control and feedback

**Date:** 2026-08-13
**Status:** accepted (pending supplier price verification)

## Context

The 3-DOF planar manipulator (ADR 005) is not a positioning device — it is half of a
dynamics experiment. Both control approaches place hard requirements on the joints:

- **Feedforward momentum compensation** needs the arm's reaction torque to be *predicted*,
  which requires the joints to track a commanded trajectory accurately enough that the
  prediction is valid.
- **Reaction null-space planning** produces a **joint velocity** command — the null-space
  projection is computed in q̇ — so the joints must accept and track velocity commands.

Both also need joint position feedback at control rate to evaluate H_bm(q), which is
configuration-dependent.

The arm moves in the horizontal plane, so joints never hold against gravity. Holding
torque is therefore not the sizing constraint; command fidelity is.

## Options considered

1. **Smart serial servos (Dynamixel XL330 class)** — native velocity control mode,
   position/velocity/load feedback, daisy-chained TTL half-duplex bus, ~0.5 N·m stall at
   this size (far more than a light planar arm needs). Driven from a G474 UART through a
   direction-control buffer.
2. **Hobby RC servos** — rejected. A PWM position command into an opaque internal control
   loop gives no velocity command, no feedback, and unknown dynamics. This breaks *both*
   controllers, not just one.
3. **Gimbal BLDC + SimpleFOC at each joint** — genuinely attractive: unified firmware with
   the reaction wheel, true torque-level control, backdrivable, and the horizontal-plane
   geometry means low direct-drive torque is acceptable. Rejected for phase 2 because it
   adds three more FOC axes to bring up, and distal motor mass inflates arm inertia at
   exactly the radius where it hurts most. Retained as a phase-4 upgrade if torque-level
   joint control becomes scientifically interesting.

## Decision

Three Dynamixel XL330-class smart servos on a shared TTL bus, in velocity-control mode,
driven from an STM32 G474 UART via a tri-state direction buffer (74LVC1G241 class).

## Consequences

- The joint interface matches what the planners emit (q̇) with no translation layer.
- Joint feedback lets H_bm(q) be evaluated from measured configuration rather than
  commanded configuration — important when tracking error is one of the things being
  characterised.
- Servo bus timing must be budgeted inside the control loop; the bus is polled, and three
  daisy-chained servos on one UART is a shared-bandwidth resource.
- Price is the largest single uncertainty in the BOM (see [budget note](../bom.md)); the
  documented fallback is option 3.
- Servo vibration and bus wiring both couple to the base — hence the isolated IMU mount
  and printed strain reliefs in [ADR 007](007-manufacturing-strategy.md).
