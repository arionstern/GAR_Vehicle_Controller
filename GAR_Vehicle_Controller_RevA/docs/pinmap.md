# Pin / Resource Map — Nucleo-G474RE

Owner: TODO (Task A). This file is the single source of truth for MCU resources.
**Claim a pin here (in a reviewed commit) before wiring it on `02_STM32_NUCLEO`
or using it in firmware.**

Pins are assigned *after* the net names in `signals.md` are agreed. Subsystem
sheets do not need a pin to be drawn; only the Nucleo sheet and layout do.
Every team that uses this STM32 claims pins in this file, not in a separate one.

No assignments have been made yet. Every row below must be checked against the
STM32G474RE datasheet alternate-function table and the Nucleo-64 user manual
(UM2505) before it is filled in.

## Functions needing assignment

| Net (`signals.md`) | Direction | Peripheral type needed | MCU pin | Peripheral / AF | Nucleo header pin | DMA / IRQ | Status |
|---|---|---|---|---|---|---|---|
| `RC_UART_RX` | in | UART RX (inversion-capable if SBUS inverted) | TODO | TODO | TODO | TODO | Unassigned |
| `RC_UART_TX` | out | UART TX (same UART as above) | TODO | TODO | TODO | TODO | Unassigned |
| `TELEM_UART_TX` | out | UART TX | TODO | TODO | TODO | TODO | Unassigned |
| `TELEM_UART_RX` | in | UART RX | TODO | TODO | TODO | TODO | Unassigned |
| `ESC_PWM` | out | Timer PWM channel | TODO | TODO | TODO | — | Unassigned |
| `SERVO_PWM` | out | Timer PWM channel | TODO | TODO | TODO | — | Unassigned |
| `LIDAR_UART_RX` / `LIDAR_UART_TX` | in/out | UART | TODO | TODO | TODO | TODO | Unassigned |
| `LIDAR_MOTOR_CTRL` | out | PWM or GPIO (TODO: verify A1 requirement) | TODO | TODO | TODO | — | Unassigned |
| `EXP_I2C_SCL` / `EXP_I2C_SDA` | bidir | I2C | TODO | TODO | TODO | — | Unassigned |
| `EXP_SPI_SCK` / `MISO` / `MOSI` / `CS` | — | SPI | TODO | TODO | TODO | — | Unassigned |
| `CAN_TX` / `CAN_RX` (reserved) | — | FDCAN | TODO | TODO | TODO | — | Unassigned |
| `VBAT_SENSE` | in | ADC | TODO | TODO | TODO | — | Unassigned |
| `EXP_ADC1` | in | ADC | TODO | TODO | TODO | — | Unassigned |
| `EXP_UART_TX` / `EXP_UART_RX` | — | UART | TODO | TODO | TODO | TODO | Unassigned |
| Spare GPIO | — | GPIO | TODO | — | TODO | — | Unassigned |

## Reserved for other teams

Reserve the *kind* of resource early (for example "one CAN bus, two ADC
inputs"); fill in exact pins when the pin map is assigned.

| Team | Resource needed | MCU pin(s) | Contact | Status |
|---|---|---|---|---|
| BMS (to be confirmed) | TODO — ask what the BMS reports and over which interface | TODO | TODO | Not yet requested |

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
| 2026-10-08 | Linked rows to `signals.md`; added reservations for other teams | — |
