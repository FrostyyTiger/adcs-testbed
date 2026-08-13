# 003: Reaction wheel drive — gimbal BLDC + FOC with position encoders

**Date:** 2026-08-11
**Status:** accepted; **sizing revised 2026-08-13 by [ADR 005](005-coupled-manipulator-scope.md)**

> **Sizing note.** Wheel momentum capacity is now set by the *arm's* reaction momentum
> rather than by base slew rate. Target inertia rose to ≥ 3×10⁻⁵ kg·m², roughly 2.5× the
> figure implied below, achieved through larger rim radius and steel dowel ballast. The
> drive architecture — gimbal BLDC, FOC, torque mode — is unchanged and matters more than
> ever, since the wheel must now absorb transient arm reactions rather than merely slew a
> passive base.

## Context

A reaction wheel is torque-controlled and must pass smoothly through zero speed with
frequent direction reversals — the hardest operating regime for brushless drives. Hobby
ESCs (sensorless trapezoidal commutation, unidirectional or clunky reversing) are
unusable here. The realistic options are field-oriented control (FOC) with a rotor
position sensor, or motors with integrated speed-mode drivers.

## Options considered

1. **Gimbal BLDC (GM2804/GM3506 class) + SimpleFOC driver + magnetic encoder** — gimbal
   motors have high winding resistance (safe at low PWM duty, smooth at low speed) and
   are designed for exactly this torque-at-low-speed regime. SimpleFOC in torque mode
   gives a clean `commanded torque → wheel` interface, which is what the control loop
   wants. Needs an AS5600/AS5048A magnetic encoder per motor and FOC tuning work.
2. **Integrated-driver BLDC (Nidec 24H style)** — PWM speed command, driver on the motor.
   Simple, common in hobby reaction-wheel builds, but speed-mode control wraps the torque
   loop we actually want inside an opaque speed loop, and zero-crossing behaviour is poor.
3. **Hobby ESC + outrunner** — rejected outright for the zero-speed reasons above.

## Decision

Option 1: one GM2804-class gimbal motor, a SimpleFOC-compatible driver (SimpleFOC Mini or
Shield), and a diametric-magnet encoder on the shaft, run in closed-loop torque (current)
mode from the G474.

## Consequences

- The controller commands torque directly — the plant model in `sim/` stays honest, and
  the same interface scales to 3 wheels.
- FOC bring-up (sensor alignment, current tuning) is a real work package; it is scheduled
  first, before any attitude control, and gets its own build-log entries.
- Wheel discs are printed with a steel or brass rim mass (e.g. M3 screws or press-fit
  dowels at the perimeter) to maximise inertia per gram; the motor only ever sees a
  balanced, rigid disc. Balance matters: print, then trim mass until vibration at speed
  is acceptable.
- Encoder mounting (concentric diametric magnet, controlled air gap) is a precision
  feature of the motor-mount CAD, not an afterthought.
