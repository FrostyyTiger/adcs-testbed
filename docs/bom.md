# Bill of materials (draft)

> **Status: draft, 2026-08-11.** Prices are estimates in CHF incl. VAT and need
> verification before ordering. Swiss/EU-friendly suppliers: **Mouser CH** (CHF pricing,
> free shipping ≥ 50 CHF), **Distrelec**, **Bastelgarage.ch**, **Play-Zone.ch**,
> **Berrybase / Reichelt** (DE, ship to CH), **AliExpress / iFlight** (gimbal motors —
> few EU stock options exist for these).

## Phase 1 — single-axis (yaw) demonstrator

| # | Part | Qty | Est. CHF | Supplier | Notes |
|---|------|-----|----------|----------|-------|
| 1 | STM32 Nucleo-G474RE | 1 | 25 | Mouser / Distrelec | Flight computer ([ADR 002](decisions/002-microcontroller.md)) |
| 2 | Gimbal motor GM2804 (or GM3506) | 1 | 25–40 | AliExpress / iFlight EU dealers | Hollow-shaft version eases encoder magnet mounting |
| 3 | SimpleFOC Mini (DRV8313) or SimpleFOC Shield v2 | 1 | 15–30 | simplefoc.com shop (EU), AliExpress | Shield stacks directly on the Nucleo |
| 4 | AS5600 magnetic encoder breakout + diametric magnet | 1 | 8 | Bastelgarage / Berrybase | AS5048A (~18, SPI) if I2C proves too slow |
| 5 | 9-DoF IMU: Adafruit LSM6DSOX + LIS3MDL breakout | 1 | 25 | Bastelgarage / Mouser / Play-Zone | Raw gyro/accel/mag → own estimator, no black-box fusion |
| 6 | ESP32 DevKitC | 1 | 12 | Bastelgarage / Berrybase | UART→WiFi telemetry bridge only |
| 7 | 2S LiPo 450–800 mAh | 2 | 25 | Brack / Galaxus / hobby shops | One flying, one charging |
| 8 | LiPo charger (B6-class) | 1 | 35 | Galaxus / hobby shops | Skip if the rocketry team days left you one |
| 9 | Buck converter 5 V + 3.3 V rails | 2 | 10 | Bastelgarage / Berrybase | Or one 5 V buck + Nucleo LDO |
| 10 | Rotary stand bearing (608ZZ pair or 120 mm lazy-susan) | 1 | 15 | Hardware store / Amazon.de | See open questions below |
| 11 | Connectors (XT30, JST-XH kit), silicone wire | — | 30 | Bastelgarage / Mouser | No loose jumpers on the rotating platform |
| 12 | Fasteners M2/M3 kit, threaded inserts | — | 25 | Bastelgarage / Amazon.de | Heat-set inserts print-friendly |
| 13 | Wheel rim mass: M3×10 screws / steel dowel pins | — | 10 | Hardware store | Perimeter mass for the printed disc |
| 14 | PETG/PLA filament | 1 | 25 | Already have printer | Structure + wheels + tray + stand |

**Phase 1 subtotal: ≈ 285–320 CHF**

## Phase 2 — 3-axis expansion

| # | Part | Qty | Est. CHF | Notes |
|---|------|-----|----------|-------|
| 15 | Gimbal motor (same as #2) | 2 | 50–80 | Order all 3 at once if shipping dominates |
| 16 | FOC driver (same as #3, or B-G431B-ESC1 per wheel) | 2 | 30–45 | ESC1 (~20/pc, Mouser) offloads FOC per wheel |
| 17 | Encoder breakout + magnet | 2 | 16 | |
| 18 | 3-DOF stand: string suspension kit or spherical bearing | 1 | 20–150 | String/wire torsion mount is cheap and good enough |
| 19 | Bigger battery (3S 650–1000 mAh) if power budget demands | 1 | 20 | |

**Phase 2 subtotal: ≈ 135–310 CHF** → **project total ≈ 420–630 CHF**, inside the
400–800 CHF envelope with margin for a v2 PCB run (~60–100 CHF at JLC/Aisler).

## Optional / nice-to-have

| Part | Est. CHF | Why |
|------|----------|-----|
| BNO085 breakout | 30 | Independent fused-attitude "truth" reference to validate your own EKF against |
| Current-sense-capable driver upgrade | — | True current-mode FOC torque control vs voltage-mode approximation |
| nRF24L01+ pair | 10 | Telemetry fallback if WiFi latency/jitter annoys |

## Open sourcing questions

- **Gimbal motor sourcing** is the weak point for EU buying — mostly AliExpress. Check
  iFlight EU resellers and eBay.de before defaulting to AliExpress lead times.
- **Stand choice** (Phase 1): 608 bearings on a printed shaft vs lazy-susan ring —
  measure breakaway friction torque of whichever arrives first; it sets the disturbance
  floor the controller must beat.
