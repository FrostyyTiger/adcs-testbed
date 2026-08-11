# 002: Microcontroller — STM32 (Nucleo-G474RE), ESP32 as telemetry bridge

**Date:** 2026-08-11
**Status:** accepted

## Context

Candidates: ESP32, STM32, Teensy 4.x. Requirements: run FOC motor control (via SimpleFOC)
plus a 100–500 Hz attitude/control loop with headroom for a later Kalman filter; hardware
FPU; good timer/encoder peripherals; wireless telemetry (the free-rotating cube cannot be
tethered — a cable is a large disturbance torque at this scale). The author already has
STM32 HAL and Arduino/PlatformIO experience. The project is a portfolio piece for
aerospace roles, where STM32 is the de-facto industry MCU family.

## Options considered

1. **STM32 Nucleo-G474RE** — 170 MHz Cortex-M4F, timers designed for motor control,
   CORDIC/FMAC accelerators (trig for FOC and quaternion math in hardware), first-class
   SimpleFOC support via stm32duino/PlatformIO, and a professional path (CubeIDE/HAL,
   debugging via on-board ST-LINK) already familiar to the author. No radio on board.
2. **ESP32** — built-in WiFi solves telemetry, huge community; but weaker timer/ADC
   peripherals for FOC, RF-related jitter concerns in the control loop, and less
   aerospace-industry relevance.
3. **Teensy 4.1** — fastest of the three, excellent Arduino support; but NXP iMXRT
   toolchain is less standard in industry, no radio, higher price, and no on-board
   debugger.

## Decision

STM32 Nucleo-G474RE as the flight computer, developed under PlatformIO (stm32duino core)
so SimpleFOC is available, with the option to drop to HAL/CMSIS for specific peripherals.
A bare ESP32 module rides along as a UART-to-WiFi telemetry bridge only — it runs no
control code.

## Consequences

- One firmware handles estimation, control, and FOC for phase 1 (single motor). If CPU
  headroom gets tight with three motors, per-wheel driver MCUs (e.g. ST B-G431B-ESC1,
  which pairs a G431 with a motor driver for ~20 CHF) are a clean distributed-architecture
  escape hatch — and mirror how real ADCS wheel electronics are organised.
- Telemetry protocol must be designed link-agnostic (framed packets over UART) so the
  bridge is swappable (WiFi UDP now, BLE or an nRF24 later if WiFi jitter annoys).
- ST-LINK debugging works only on the bench; on the stand, debugging happens over
  telemetry logs.
