# ADCS Testbed

A desktop CubeSat attitude determination and control system (ADCS) demonstrator: a 3D-printed 1U (10 cm) cube on a low-friction stand that senses its own orientation with an IMU + magnetometer and autonomously rotates itself to commanded attitudes using reaction wheels (brushless motors with weighted discs). Control runs on a microcontroller — PID first, Kalman-filter attitude estimation later — with target capabilities of point-and-hold with disturbance rejection, detumble, and target tracking. Fully open source (MIT), built as a portfolio project over ~3–4 months on a hobbyist budget.

## Status

🚧 Early design phase — architecture and component selection in progress.

## Repository layout

| Directory | Contents |
| --- | --- |
| [`docs/`](docs/) | Design decisions, bill of materials, build log |
| [`firmware/`](firmware/) | Microcontroller code (control loop, estimation, drivers) |
| [`sim/`](sim/) | Python simulation, control design and tuning |
| [`cad/`](cad/) | 3D-printable structure and mechanical parts |
| [`hardware/`](hardware/) | Schematics, wiring, PCB files |

## License

[MIT](LICENSE)
