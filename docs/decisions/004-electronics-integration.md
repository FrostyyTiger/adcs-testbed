# 004: Electronics — breakout boards first, custom PCB as v2 stretch goal

**Date:** 2026-08-11
**Status:** accepted

## Context

The author has PCB design experience (student rocketry avionics), so a custom board is
feasible — but a respin cycle costs 2–4 weeks and the project's value is in demonstrated
control, not board count.

## Decision

Phase 1 flies as a Nucleo + breakout boards on a 3D-printed electronics tray with proper
connectors (JST-XH / XT30, no loose jumper wires on the rotating platform). A custom
integration PCB (MCU + IMU + three motor drivers + power) is a v2 goal once 3-axis
control works, and is the natural place to spend leftover budget and showcase PCB skills.

## Consequences

- Wiring discipline substitutes for a PCB: a wiring diagram in `hardware/` is kept
  current, and every connector is labelled.
- Mass and volume are wasted vs a PCB; acceptable at 1U desktop scale.
- The tray CAD should reserve a footprint the size of a future 90×90 mm board so the
  v2 PCB is a drop-in swap.
