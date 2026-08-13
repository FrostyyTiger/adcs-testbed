# Documentation

- [`glossary.md`](glossary.md) — plain-English definitions of every term used here.
  Start here if the vocabulary is unfamiliar
- [`roadmap.md`](roadmap.md) — phases, exit criteria, standing risks
- [`decisions/`](decisions/) — architecture decision records: one file per significant
  design choice, with the options considered and the rationale
- [`bom.md`](bom.md) — bill of materials with suppliers and estimated prices
- [`figures/`](figures/) — drawings and diagrams referenced from the docs
- `build-log/` — dated build log entries with photos (starts with phase 1)

## Decision records

| # | Decision |
| --- | --- |
| [001](decisions/001-single-axis-first.md) | Single-axis base (reframed by 005) |
| [002](decisions/002-microcontroller.md) | STM32 G474 flight computer, ESP32 telemetry bridge |
| [003](decisions/003-reaction-wheel-drive.md) | Gimbal BLDC + FOC wheel drive (sizing revised by 005) |
| [004](decisions/004-electronics-integration.md) | Breakout boards first, custom PCB as v2 |
| [005](decisions/005-coupled-manipulator-scope.md) | Scope: coupled attitude + manipulator testbed, yaw-only base |
| [006](decisions/006-arm-actuators.md) | Smart servos for the arm joints |
| [007](decisions/007-manufacturing-strategy.md) | FDM printing as project instrumentation |
