# Bill of materials (draft)

> **Status: draft, 2026-08-13.** Prices are estimates in CHF incl. VAT and need
> verification before ordering. Swiss/EU-friendly suppliers: **Mouser CH** (CHF pricing,
> free shipping ≥ 50 CHF), **Distrelec**, **Bastelgarage.ch**, **Play-Zone.ch**,
> **Berrybase / Reichelt** (DE, ship to CH), **Generation Robots / RobotShop EU**
> (Dynamixel), **AliExpress / iFlight** (gimbal motors — few EU stock options exist).

Scope follows [ADR 005](decisions/005-coupled-manipulator-scope.md): yaw-only base with
one reaction wheel, plus a 3-DOF planar arm. Three-axis operation and the air bearing are
phase 3 and deliberately out of budget.

## Phase 1 — base attitude control

| # | Part | Qty | Est. CHF | Supplier | Notes |
|---|------|-----|----------|----------|-------|
| 1 | STM32 Nucleo-G474RE | 1 | 25 | Mouser / Distrelec | Flight computer ([ADR 002](decisions/002-microcontroller.md)) |
| 2 | Gimbal motor GM2804 (or GM3506) | 2 | 50–80 | AliExpress / iFlight EU dealers | One flying, one spare — long lead time, and FOC bring-up can cook a motor. Hollow shaft eases encoder magnet mounting |
| 3 | SimpleFOC Mini (DRV8313) or SimpleFOC Shield v2 | 1 | 15–30 | simplefoc.com shop (EU), AliExpress | Shield stacks directly on the Nucleo |
| 4 | AS5600 magnetic encoder breakout + **diametric** magnet | 1 | 8 | Bastelgarage / Berrybase | Must be diametrically magnetised, not axial. AS5048A (~18, SPI) if I2C proves too slow |
| 5 | 9-DoF IMU: LSM6DSOX + LIS3MDL breakout | 1 | 25 | Bastelgarage / Mouser / Play-Zone | Raw gyro/accel/mag → own estimator, no black-box fusion |
| 6 | ESP32 DevKitC | 1 | 12 | Bastelgarage / Berrybase | UART→WiFi telemetry bridge only, runs no control code |
| 7 | 2S LiPo 450–800 mAh | 2 | 25 | Brack / Galaxus / hobby shops | One flying, one charging |
| 8 | LiPo charger (B6-class) | 1 | 35 | Galaxus / hobby shops | Skip if one is already on hand |
| 9 | Buck converters, 5 V and 3.3 V rails | 2 | 10 | Bastelgarage / Berrybase | |
| 10 | 608ZZ bearings (pair) for the yaw stand | 1 | 10 | Hardware store | Printed housing; dry-cleaned races reduce breakaway friction |
| 11 | Connectors (XT30, JST-XH kit), silicone wire | — | 30 | Bastelgarage / Mouser | No loose jumpers on the rotating platform |
| 12 | Fasteners M2/M3 kit, heat-set threaded inserts | — | 25 | Bastelgarage / Amazon.de | |
| 13 | Wheel ballast: steel dowel pins / M3 screws | — | 15 | Hardware store | Rim mass at max radius; up-sized per ADR 005 |

**Phase 1 subtotal: ≈ 285–320 CHF**

## Phase 2 — manipulator and coupled control

| # | Part | Qty | Est. CHF | Supplier | Notes |
|---|------|-----|----------|----------|-------|
| 14 | Dynamixel XL330-class smart servo | 3 | 90–180 | Generation Robots / RobotShop EU | **Largest price uncertainty in the plan — verify before committing.** Fallback is gimbal BLDC + FOC joints ([ADR 006](decisions/006-arm-actuators.md)) |
| 15 | TTL half-duplex direction buffer + bus wiring | 1 | 15 | Mouser | 74LVC1G241 class |
| 16 | 5 V supply capacity for the servo bus | 1 | 10 | Bastelgarage | |

**Phase 2 subtotal: ≈ 115–205 CHF**

## Consumables

| Part | Est. CHF | Notes |
|------|----------|-------|
| PETG (structure), PLA-CF (arm links), TPU (isolation mounts), PVA (support) | 100–130 | Per [ADR 007](decisions/007-manufacturing-strategy.md) |
| Hardened nozzle | 15 | Required for CF- or metal-filled filament |

## Projected total

**≈ 515–670 CHF** for phases 1 and 2 including consumables — inside the 400–800 CHF
envelope with roughly 130–285 CHF of headroom.

Deferred to phase 3 and explicitly outside this budget: planar air bearing, two additional
reaction wheel assemblies, 4+ DOF arm, custom integration PCB.

## Optional / nice-to-have

| Part | Est. CHF | Why |
|------|----------|-----|
| BNO085 breakout | 30 | Independent fused-attitude reference to validate the in-house estimator against |
| nRF24L01+ pair | 10 | Telemetry fallback if WiFi latency or jitter proves troublesome |

## Open sourcing questions

- **Dynamixel pricing** is the single largest budget uncertainty; verify across EU
  distributors before committing.
- **Gimbal motor sourcing** is the weak point for EU buying — mostly AliExpress. Check
  iFlight EU resellers and eBay.de first, and order early: this is the long-lead item.
- **Bearing friction** must be measured once the stand is printed; it sets the disturbance
  floor the controller has to beat and is quoted in every later result.
