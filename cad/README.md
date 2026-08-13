# CAD

Source CAD plus exported STL/3MF with Bambu Lab print profiles. Design rationale and
material choices are in [ADR 007](../docs/decisions/007-manufacturing-strategy.md).

## Parts

**Structure**
- 1U cube frame and removable panels
- Electronics tray (reserves a 90×90 mm footprint for the future integration PCB)
- Yaw stand: 608 bearing housing, shaft, levelling feet

**Actuation**
- Reaction wheel disc with steel dowel pockets at maximum radius, plus trim-balance pockets
- Motor mount with shimmable, controlled encoder air gap
- 3-DOF planar arm links — **interchangeable sets with different mass distributions**, so
  arm-to-base inertia ratio is a swept experimental variable

**Multi-material**
- TPU-isolated IMU mount (rigid shell, flexible grommets, printed as one part)
- Cable strain reliefs and service loops

**Test and calibration fixtures**
- Trifilar pendulum rig for moment-of-inertia measurement
- Encoder magnet concentricity jig
- IMU calibration cube (orthogonal reference faces)
- Repeatable pendulum disturbance injector (fixed-detent release, known angular impulse)

## Conventions

- Heat-set M3 inserts on anything opened repeatedly; no self-tapping into plastic
- Parts oriented so wheel hoop stress and joint loads act in-plane, not across layers
- Axis colour coding printed in: X red, Y green, Z blue
- Every part weighed as printed and its mass recorded, to validate CAD mass properties
