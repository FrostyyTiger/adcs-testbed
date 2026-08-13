# Coupled Attitude and Manipulator Control Testbed

A 1U-scale spacecraft attitude control platform with a mounted 3-DOF planar manipulator, built to study the disturbance coupling between arm motion and base attitude that governs on-orbit servicing and debris capture. The base carries a reaction wheel driven by a brushless motor under field-oriented control, estimates its attitude from a 9-DOF IMU and magnetometer through a complementary filter, and rotates freely on a low-friction yaw bearing to approximate free-floating dynamics in the rotational axis. Moving the arm imparts a reaction torque on the base, so the control problem is not attitude hold in isolation but the joint problem of planning arm trajectories and wheel commands together. The system implements two approaches for comparison: **feedforward momentum compensation**, where the wheel absorbs the predicted arm reaction torque, and **reaction null-space planning**, where joint trajectories are constrained to produce zero net angular momentum transfer to the base. Performance is characterised by pointing error during commanded arm slews, wheel momentum accumulation across a manoeuvre sequence, and the saturation margin remaining after repeated operations. Hardware is 3D printed in PETG with off-the-shelf actuators; firmware, CAD, and analysis tooling are open source.

## Status

🚧 **Design phase.** Architecture and component selection are settled (see
[decisions](docs/decisions/)); simulation work is starting. No hardware built yet.

## Why this problem

A spacecraft reaching out to grab something — a servicing vehicle docking with a failed
satellite, a capture vehicle closing on debris — has nothing to brace against. Extending
the arm rotates the spacecraft, breaking the pointing the manipulator's own cameras and
the communications link depend on. Real missions (ETS-VII, MEV-1/2, ESA's ADRIOS with
ClearSpace) have to solve this, and the algorithms are mature; what is scarce is cheap,
open hardware on which to compare them under measured conditions. That is what this
testbed is for.

## The core result being pursued

Two strategies for the same disturbance, measured against each other on identical hardware:

| Approach | Idea | Expected cost |
| --- | --- | --- |
| Feedforward momentum compensation | Wheel absorbs the arm's predicted reaction torque; arm path unconstrained | Consumes wheel momentum → saturation limits how many manoeuvres you get |
| Reaction null-space planning | Constrain joint velocities to the null space of the coupling inertia so the base sees zero reaction | Consumes no wheel momentum, but restricts achievable arm trajectories |

Plus a third, uncompensated baseline: with the wheel idle, a 90° arm slew swings the base
by roughly 22–66° depending on arm mass, since a printed arm at this scale has inertia
comparable to the entire base. Details in [ADR 005](docs/decisions/005-coupled-manipulator-scope.md).

## Repository layout

| Directory | Contents |
| --- | --- |
| [`docs/`](docs/) | Design decisions (ADRs), roadmap, bill of materials, build log |
| [`firmware/`](firmware/) | Microcontroller code: FOC, estimation, control, planners, telemetry |
| [`sim/`](sim/) | Python simulation of the coupled dynamics; control design and tuning |
| [`cad/`](cad/) | 3D-printable structure, wheel, arm links, stand, and test fixtures |
| [`hardware/`](hardware/) | Schematics, wiring, power budget |

Start with the [roadmap](docs/roadmap.md) and the [decision records](docs/decisions/).

## License

[MIT](LICENSE)
