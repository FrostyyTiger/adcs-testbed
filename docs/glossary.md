# Glossary

Plain-English definitions of the terms used throughout this repository, in the sense they
carry *here*. Roughly ordered from the mission problem down to the hardware.

## The problem this testbed studies

**On-orbit servicing** — a spacecraft doing physical work on another spacecraft in orbit:
refuelling, repairing, boosting a dying satellite to a new orbit. Requires a manipulator
and precise relative pointing.

**Active debris removal (ADR)** — capturing defunct satellites or rocket stages and
deorbiting them. ESA's ADRIOS mission with ClearSpace is the flagship European example.
*Note the acronym collision: in this repository, "ADR" in a filename or a link always
means Architecture Decision Record (see below), never active debris removal.*

**Free-floating** — a spacecraft whose attitude actuators are off, so its total angular
momentum is conserved and any arm motion rotates the body. **Free-flying** is the same
system with base actuators active, holding attitude against the arm. This testbed
demonstrates both: the uncompensated baseline is free-floating, and the compensated cases
are free-flying. Reaction null-space planning is notable precisely because it works
*without* needing base actuation at all.

**CubeSat / 1U** — a small-satellite standard built from 10 × 10 × 10 cm units. "1U" is one
such cube, and it sets the scale of this testbed.

## Attitude and control

**Attitude** — which way a spacecraft is pointing, i.e. its orientation. Not its position.

**ADCS (Attitude Determination and Control System)** — the subsystem that figures out
which way the spacecraft is pointing (determination) and makes it point somewhere else
(control).

**Slew** — a commanded rotation from one attitude to another.

**Point-and-hold** — rotate to a commanded attitude and stay there despite disturbances.

**Detumble** — kill residual rotation rates after deployment or an upset. Simpler than
attitude control: it only needs to drive angular *rate* to zero, so no attitude estimate
is required.

**Disturbance torque** — any unwanted torque acting on the body. In orbit: gravity
gradient, solar pressure, residual atmosphere. On this testbed: bearing friction, air
currents, cable stiffness — and, deliberately, the arm.

**Disturbance rejection** — how well the controller holds attitude while a disturbance
pushes on it. Measured here with a repeatable printed pendulum impulse rather than by
hand.

**PID control** — the standard feedback law: correct in proportion to the current error
(P), its accumulated history (I), and its rate of change (D).

**Feedforward** — commanding a correction for a disturbance you can *predict*, rather than
waiting to measure the resulting error. Faster than feedback, but only as good as the
model it predicts from.

## Actuation

**Reaction wheel** — a weighted disc spun by a motor inside the spacecraft. Spinning it one
way rotates the body the other way. This is the *muscle* of the system.

**Momentum exchange** — the principle a reaction wheel works on. Motor torque is internal,
so it can only move angular momentum between wheel and body; it never creates any. This is
why a satellite can turn with no fuel and nothing to push against.

**Momentum saturation** — a reaction wheel has a maximum speed. Once it reaches it, the
wheel can absorb no more momentum and the spacecraft loses control authority in that axis.

**Saturation margin** — how much wheel capacity is left before that happens. One of this
project's three headline metrics, because a controller that points perfectly but saturates
after two manoeuvres is not useful.

**Momentum dumping (desaturation)** — using an *external* torque (magnetorquers pushing on
Earth's magnetic field, or thrusters) to bleed off accumulated wheel momentum. This testbed
has no external torque source, so it desaturates by hand — the honest limitation that
makes saturation margin worth measuring.

**BLDC (brushless DC motor)** — a motor with no brushes, commutated electronically. Needs a
driver that knows where the rotor is.

**Gimbal motor** — a BLDC with many poles and high winding resistance, designed for smooth
torque at very low speed. The right choice for a reaction wheel, which constantly reverses
direction through zero speed, where ordinary drone motors cog and stall.

**FOC (field-oriented control)** — a motor control method that continuously orients the
stator current relative to the rotor magnets for maximum smooth torque at any speed,
including zero. Requires a rotor position sensor.

**Torque mode vs. speed mode** — a torque-mode driver takes "apply this much torque" as its
command; a speed-mode driver takes "spin at this rate." Control theory wants torque, since
that is what appears in the equations of motion; cheap integrated motor drivers usually
offer only speed.

## Sensing and estimation

**IMU (Inertial Measurement Unit)** — a chip package combining a gyroscope and an
accelerometer. The *inner ear* of the system.

**Gyroscope** — in modern hardware, a MEMS sensor chip that reports angular *rate* ("you are
turning at 12°/s"). It does not spin. *Easy confusion worth stating plainly: the spinning
disc in this cube is the reaction wheel — an actuator — not the gyroscope, which is a
stationary sensor chip.*

**Accelerometer** — reports linear acceleration, including gravity, which makes it a
tilt reference when the system is not accelerating.

**Magnetometer** — measures the local magnetic field, giving an absolute heading reference
much as a compass does. Noisy, and easily distorted by the testbed's own motor currents.

**Gyro drift / bias** — a gyroscope's small zero-point error. Integrating rate to get angle
integrates that error too, so angle estimated from the gyro alone slowly wanders away from
truth.

**Sensor fusion** — combining sensors whose errors differ so the result is better than any
one of them. Here: the gyro is smooth but drifts, the magnetometer is absolute but noisy.

**Complementary filter** — the simplest useful fusion: trust the gyro over short timescales
(high-pass) and the absolute reference over long ones (low-pass), and add the two. The
blend is hand-tuned.

**Kalman filter / MEKF** — the same idea with the blend computed optimally from the
statistics of the noise rather than hand-picked. The *multiplicative* extended Kalman
filter (MEKF) is the standard spacecraft variant, structured to respect the fact that
attitude quaternions must stay unit-length.

**Encoder** — a sensor reporting shaft angle. Here a magnetic encoder chip reads a small
magnet on the motor shaft.

**Diametric magnet** — a magnet polarised across its diameter (N on one side, S on the
other) rather than along its axis. Magnetic encoders require this; an axially magnetised
magnet looks identical and simply will not work.

**Telemetry** — measurements streamed off the system while it runs. Essential here because
the cube rotates freely and cannot be tethered to a debugger.

## The coupled problem — the core of this project

**Manipulator** — a robot arm. This one has three joints, all moving in a horizontal plane.

**Reaction torque** — move the arm and the base twists the other way, for exactly the same
momentum-conservation reason a reaction wheel works. This is the disturbance the whole
project exists to study.

**Moment of inertia** — how hard an object is to angularly accelerate, the rotational
equivalent of mass. Depends strongly on how far the mass sits from the axis (as radius
squared), which is why a reaction wheel puts its mass at the rim.

**Coupling inertia matrix (H_bm)** — the matrix mapping arm joint *rates* to the angular
momentum they impart to the base. It depends on the arm's current configuration, so it is
recomputed continuously. Everything below follows from it.

**Feedforward momentum compensation** — approach one. Predict the arm's reaction from
H_bm·q̇ and spin the wheel to absorb it as it happens. The arm may move however it likes,
but every manoeuvre spends wheel momentum.

**Reaction null space** — the set of joint motions that produce *zero* net momentum
transfer to the base, i.e. the null space of H_bm. With three joints and a single
controlled base axis it is two-dimensional, so useful motions exist.

**Reaction null-space planning** — approach two. Constrain the arm to move only within that
null space, so the base is never disturbed in the first place. Costs no wheel momentum, but
restricts which arm trajectories are achievable.

**Redundancy** — having more joints than the task strictly requires. It is what makes a
null space exist at all: with three joints, reaching a point in the plane uses two degrees
of freedom and leaves one spare to spend on the zero-reaction constraint.

## Test, measurement, and fabrication

**Air bearing** — a support that floats an object on a thin film of pressurised air,
approximating frictionless motion. Spherical versions give three rotational degrees of
freedom; planar versions float a puck on a flat plate. Both are out of budget here; this
testbed uses a low-friction mechanical bearing on a single axis instead.

**Breakaway friction** — the torque needed to *start* something moving from rest, higher
than the friction once it is moving. On this testbed it sets the floor on achievable
pointing accuracy, so it is measured rather than assumed.

**Trifilar pendulum** — a plate hung on three strings. Its twisting oscillation period
depends on its moment of inertia, so it is a cheap and accurate way to *measure* a part's
inertia and check it against the CAD model.

**Balancing** — removing mass asymmetry from a spinning wheel. An unbalanced reaction wheel
vibrates the whole testbed and corrupts the IMU readings the controller depends on.

**FDM / AMS** — fused deposition modelling, the filament-based 3D printing used for every
mechanical part here; the AMS is the printer's multi-material unit, which allows rigid and
flexible plastics in a single part.

## Project conventions

**ADR (Architecture Decision Record)** — a short document capturing one significant design
decision: the context, the options considered, the choice, and its consequences. Kept in
[`docs/decisions/`](decisions/) and numbered in order. Superseded decisions are annotated
rather than deleted, so the reasoning history stays readable.

**Phase 0–3** — the project's staging, from simulation through base attitude control, the
coupled study, and stretch goals. See the [roadmap](roadmap.md).
