# Pin / Resource Map — Nucleo-G474RE

Owner: TODO (Task A). This file is the single source of truth for MCU resources.
**Claim a pin here (in a reviewed commit) before using it in a schematic or in firmware.**

No assignments have been made yet. Every row below must be checked against the
STM32G474RE datasheet alternate-function table and the Nucleo-64 user manual
(UM2505) before it is filled in.

## Functions needing assignment

| Function | Direction | Peripheral type needed | MCU pin | Peripheral / AF | Nucleo header pin | DMA / IRQ | Status |
|---|---|---|---|---|---|---|---|
| RC receiver RX (SBUS/CRSF in) | in | UART RX (inversion-capable if SBUS inverted) | TODO | TODO | TODO | TODO | Unassigned |
| RC receiver TX (CRSF out) | out | UART TX (same UART as above) | TODO | TODO | TODO | TODO | Unassigned |
| ESP32 telemetry TX | out | UART TX | TODO | TODO | TODO | TODO | Unassigned |
| ESP32 telemetry RX | in | UART RX | TODO | TODO | TODO | TODO | Unassigned |
| ESC command | out | Timer PWM channel | TODO | TODO | TODO | — | Unassigned |
| Servo command | out | Timer PWM channel | TODO | TODO | TODO | — | Unassigned |
| LiDAR UART RX/TX | in/out | UART | TODO | TODO | TODO | TODO | Unassigned |
| LiDAR motor control | out | PWM or GPIO (TODO: verify A1 requirement) | TODO | TODO | TODO | — | Unassigned |
| I2C expansion SCL/SDA | bidir | I2C | TODO | TODO | TODO | — | Unassigned |
| SPI expansion SCK/MISO/MOSI/CS | — | SPI | TODO | TODO | TODO | — | Unassigned |
| CAN TX/RX (reserved) | — | FDCAN | TODO | TODO | TODO | — | Unassigned |
| Battery voltage sense | in | ADC | TODO | TODO | TODO | — | Unassigned |
| Spare ADC | in | ADC | TODO | TODO | TODO | — | Unassigned |
| Spare UART | — | UART | TODO | TODO | TODO | — | Unassigned |
| Spare GPIO | — | GPIO | TODO | — | TODO | — | Unassigned |

## Pins reserved by the Nucleo board itself

TODO: list from UM2505 and confirm against the physical board's solder-bridge
configuration — ST-LINK virtual COM port UART, SWD, user LED, user button,
HSE/LSE oscillator pins. Do not reuse these without a documented reason.

## Resource summary

Fill these in as pins are assigned so conflicts are visible at a glance.

| Resource | Used by |
|---|---|
| USARTx / UARTx / LPUART1 | TODO |
| TIMx channels | TODO |
| DMA channels | TODO |
| ADCx inputs | TODO |
| FDCANx | TODO |

## Change log

| Date | Change | By |
|---|---|---|
| 2026-10-06 | File created, no assignments | — |
