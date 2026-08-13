# Roadmap

Four phases. Phase 1 is a complete, presentable project on its own; phase 2 is where the
research contribution lives; phase 3 is explicitly beyond the current budget.

## Phase 0 — simulation (in progress, no hardware required)

- Plant model: yaw base + reaction wheel + 3-DOF planar arm.
  Base `I_b·θ̈_b = −τ_w + τ_dist`, wheel `I_w·ω̇_w = τ_motor − friction`, with angular
  momentum about the pivot conserved and the arm contributing `H_bm(q)·q̇`.
- Construct the coupling inertia `H_bm(q)` from link mass properties.
- Implement all three cases: uncompensated baseline, feedforward momentum compensation
  (`h_w = −H_bm(q)·q̇`), and reaction null-space planning (desired end-effector velocity
  projected into `null(H_bm)`).
- Add sensor noise, quantisation, and joint tracking error to find what actually limits
  each approach.
- Produce the three metrics in simulation: pointing error, momentum accumulation across a
  manoeuvre sequence, saturation margin.

**Exit criterion:** both planners working with plots, and a defensible prediction of what
the hardware should do.

## Phase 1 — base attitude control (~6–8 weeks)

1. **FOC bring-up on the bench** — encoder alignment, current tuning, smooth torque
   through zero speed. Highest-risk item; done before anything else is attached.
2. Print and balance the reaction wheel; verify momentum capacity and vibration vs. speed.
3. Stand: print the bearing housing, then **measure breakaway and running friction** —
   this number is the disturbance floor every later result is quoted against.
4. IMU driver and calibration (printed 6-position fixture); complementary filter for yaw.
5. Telemetry over the ESP32 bridge, with live plotting.
6. PID point-and-hold and detumble (rate damping only). Quantify pointing accuracy and
   disturbance rejection using the printed pendulum impulse injector.

**Exit criterion:** holds a commanded yaw angle to a stated tolerance and recovers from a
repeatable known disturbance, with logged plots.

## Phase 2 — manipulator and the coupled study (~8–10 weeks)

1. Print arm links; measure mass properties on the trifilar rig and reconcile with CAD.
2. Servo bus bring-up: joint velocity control and feedback.
3. Reproduce the **uncompensated baseline** on hardware — arm slew with the wheel idle —
   and compare the measured base swing against simulation.
4. Implement feedforward momentum compensation.
5. Implement reaction null-space planning.
6. Run the comparison: pointing error during slews, momentum accumulation across a
   manoeuvre sequence, saturation margin after repeated operations.
7. **Sweep arm inertia** with interchangeable printed link sets and characterise how
   coupling severity changes the relative advantage of each approach.

**Exit criterion:** a results section with real plots, simulation-vs-hardware agreement,
and an honest account of where each approach wins and why.

## Phase 3 — stretch goals (beyond the 400–800 CHF envelope)

- Planar air bearing (float pucks on a flat plate) to add translational freedom.
- Three-axis base with a 4+ DOF arm — required for a non-trivial spatial null space.
- Custom integration PCB ([ADR 004](decisions/004-electronics-integration.md)).
- Multiplicative EKF replacing the complementary filter.
- Capture/docking demonstration against a floating target.
- Torque-controlled joints via gimbal BLDC + FOC ([ADR 006](decisions/006-arm-actuators.md)).

## Standing risks

| Risk | Mitigation |
| --- | --- |
| FOC bring-up stalls | Scheduled first, on the bench, in isolation |
| Wheel imbalance corrupts the IMU | Trim-balance using the IMU as the instrument; TPU-isolated IMU mount |
| Arm flex breaks the momentum model | Stiff CF links, low mass, modest reach; validate against measured inertias |
| Model error swamps null-space planning | Degrades gracefully — the wheel absorbs residual; report the degradation as a result |
| Wheel saturates mid-manoeuvre | Up-sized wheel, 1–2 s slews, saturation margin tracked as a metric |
| Timeline overrun | Phase 1 alone is a complete, presentable project |
