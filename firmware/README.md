# Firmware

Runs on an STM32 Nucleo-G474RE ([ADR 002](../docs/decisions/002-microcontroller.md)) under
PlatformIO. An ESP32 acts purely as a UART→WiFi telemetry bridge and runs no control code.

## Planned modules

| Module | Responsibility |
| --- | --- |
| `foc/` | Reaction wheel field-oriented control (SimpleFOC), torque-mode command interface |
| `sensors/` | IMU and magnetometer drivers, calibration, magnetic encoder |
| `estimation/` | Complementary filter for yaw; MEKF later |
| `control/` | PID attitude hold, detumble (rate damping), mode logic |
| `planner/` | Feedforward momentum compensation and reaction null-space planning |
| `arm/` | Dynamixel bus driver ([ADR 006](../docs/decisions/006-arm-actuators.md)), joint velocity command and feedback |
| `telemetry/` | Framed packets over UART, link-agnostic so the bridge is swappable |

Interfaces are kept clean and modular so components can be validated independently — and
so a future flight-software-framework port stays cheap.

Toolchain and build instructions land here once phase 1 bring-up starts.
