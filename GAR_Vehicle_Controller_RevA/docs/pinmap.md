# Pin / Resource Map — Nucleo-G474RE

Owner: TODO (Task A). This file is the single source of truth for MCU resources.
**Claim a pin here (in a reviewed commit) before wiring it on `02_STM32_NUCLEO`
or using it in firmware.**

Pins are assigned *after* the net names in `signals.md` are agreed. Subsystem
sheets do not need a pin to be drawn; only the Nucleo sheet and layout do.
Every team that uses this STM32 claims pins in this file, not in a separate one.

**Status: proposal, checked against ST's documents, awaiting review.** First
drafted from ST's pin tables for the NUCLEO_G474RE target in Mbed OS
([PeripheralPins.c](https://github.com/ARMmbed/mbed-os/blob/master/targets/TARGET_STM/TARGET_STM32G4/TARGET_STM32G474xE/TARGET_NUCLEO_G474RE/PeripheralPins.c),
[PinNames.h](https://github.com/ARMmbed/mbed-os/blob/master/targets/TARGET_STM/TARGET_STM32G4/TARGET_STM32G474xE/TARGET_NUCLEO_G474RE/PinNames.h)),
then verified against DS12288 Rev 6 (STM32G474 datasheet) and UM2505 Rev 7
(Nucleo-64 user manual). Page references for each check are in
[issue #2](https://github.com/arionstern/GAR_Vehicle_Controller/issues/2).

**Open decision:** PC5 (`RC_UART_RX`) is not 5 V-tolerant (I/O type TT_a,
DS12288 p61). See "5 V tolerance" below.

## Assignments

| Net (`signals.md`) | Dir | MCU pin | Peripheral / AF | Nucleo header pin | Notes |
|---|---|---|---|---|---|
| `RC_UART_TX` | out | PC4 | USART1_TX, AF7 | Arduino D1 | USART1 supports RXINV / TXINV / SWAP |
| `RC_UART_RX` | in | PC5 | USART1_RX, AF7 | Arduino D0 | |
| `TELEM_UART_TX` | out | PB10 | USART3_TX, AF7 | Arduino D6 | To ESP32 RX |
| `TELEM_UART_RX` | in | PB11 | USART3_RX, AF7 | Morpho CN10-18 | From ESP32 TX |
| `LIDAR_UART_TX` | out | PC10 | UART4_TX, AF5 | Morpho CN7-1 | |
| `LIDAR_UART_RX` | in | PC11 | UART4_RX, AF5 | Morpho CN7-2 | |
| `EXP_UART_TX` | out | PC12 | UART5_TX, AF5 | Morpho CN7-3 | Spare UART |
| `EXP_UART_RX` | in | PD2 | UART5_RX, AF5 | Morpho CN7-4 | |
| `ESC_PWM` | out | PC6 | TIM3_CH1, AF2 | Morpho CN10-4 | Shares TIM3 with the servo (both 50 Hz) |
| `SERVO_PWM` | out | PC7 | TIM3_CH2, AF2 | Arduino D9 | |
| `LIDAR_MOTOR_CTRL` | out | PB6 | TIM4_CH1, AF2 | Arduino D10 | PWM-capable; usable as plain GPIO if the A1 only needs on/off |
| `EXP_I2C_SCL` | bidir | PC8 | I2C3_SCL, AF8 | Morpho CN10-2 | Needs pull-ups |
| `EXP_I2C_SDA` | bidir | PC9 | I2C3_SDA, AF8 | Morpho CN10-1 | |
| `EXP_SPI_SCK` | out | PB13 | SPI2_SCK, AF5 | Morpho CN10-30 | |
| `EXP_SPI_MISO` | in | PB14 | SPI2_MISO, AF5 | Morpho CN10-28 | |
| `EXP_SPI_MOSI` | out | PB15 | SPI2_MOSI, AF5 | Morpho CN10-26 | |
| `EXP_SPI_CS` | out | PB12 | GPIO | Morpho CN10-16 | Software chip select |
| `CAN_TX` | out | PA12 | FDCAN1_TX, AF9 | Morpho CN10-12 | Reserved; needs an external transceiver |
| `CAN_RX` | in | PA11 | FDCAN1_RX, AF9 | Morpho CN10-14 | |
| `VBAT_SENSE` | in | PA0 | ADC1_IN1 | Arduino A0 | Divided battery voltage; may move to the BMS link |
| `EXP_ADC1` | in | PA1 | ADC1_IN2 | Arduino A1 | |
| Spare GPIO | — | PB0, PB1 | GPIO (also ADC1_IN15 / ADC1_IN12) | PB0: Morpho CN7-34 (also Arduino A3); PB1: Morpho CN10-24 | |

Header positions are from UM2505 Table 15 (Arduino, pp. 30–31) and Table 16
(morpho, pp. 32–33). "CN10-18" means connector CN10, pin 18. By default PC4 /
PC5 are routed to Arduino D1 / D0 (UM2505 p25).

## 5 V tolerance

From the I/O structure column of DS12288 Table 12 (legend p56: FT = 5 V
tolerant, TT = 3.6 V tolerant):

| Receive pin | Net | I/O type | 5 V-tolerant? | Source |
|---|---|---|---|---|
| PC5 | `RC_UART_RX` | TT_a | **No** (3.6 V max) | DS12288 p61 |
| PB11 | `TELEM_UART_RX` | TT_a | No (3.6 V max); fine for a 3.3 V ESP32 | DS12288 p63 |
| PC11 | `LIDAR_UART_RX` | FT_f | Yes | DS12288 p68 |

PC5 is acceptable if the receiver's output is 3.3 V, which is expected but not
confirmed. Alternative: move the receiver to USART1 on PA9 (TX) / PA10 (RX),
also AF7, where PA10 is FT_fda and 5 V-tolerant (DS12288 p66). Both are free
(Arduino D8 / D2). Decision pending.

## Why these choices

- **Avoids every pin the Nucleo board already uses** (see below), and avoids PB8,
  which doubles as the BOOT0 pin: an I2C pull-up there could start the chip in
  its bootloader at reset. This is why I2C is not on the Arduino D14 / D15 pins.
- **One peripheral per job**, so nothing is shared: USART1, USART3, UART4 and
  UART5 for the four serial links; LPUART1 stays on the ST-LINK as the debug
  console.
- **ESC and servo on one timer** (TIM3), since both use the same 50 Hz frame and
  only the pulse widths differ.
- **ADC inputs on PA0 / PA1** (Arduino A0 / A1), both on ADC1.
- **SPI2 and FDCAN1 are kept apart** so the expansion SPI bus and a future CAN
  bus can be used at the same time.

## Reserved for other teams

Reserve the *kind* of resource early (for example "one CAN bus, two ADC
inputs"); fill in exact pins when the pin map is assigned.

| Team | Resource needed | MCU pin(s) | Contact | Status |
|---|---|---|---|---|
| BMS (to be confirmed) | TODO — ask what the BMS reports and over which interface | TODO | TODO | Not yet requested. Free options: FDCAN1 above, ADC on PA0 / PA1, I2C3 |

## Pins used by the Nucleo board itself

Default solder-bridge configuration per UM2505. Do not reuse these without a
documented reason.

| Pin | Used by | Source (UM2505) |
|---|---|---|
| PA2, PA3 | ST-LINK virtual COM port (LPUART1) — debug console | p24–25 |
| PA5 | User LED LD2 | p23 |
| PA13, PA14 | SWD debug (SWDIO, SWCLK) | p29, p33 |
| PB3 | SWO debug trace | p29 |
| PC13 | User button B1 (can be moved to PA0 by SB21; off by default) | p23, p26 |
| PF0, PF1 | 24 MHz HSE crystal X3 | p22 |
| PC14, PC15 | 32.768 kHz LSE crystal X2 (fitted by default; confirm on our board) | p23 |
| PB8 | BOOT0 (shared function); the Nucleo uses the option bit by default — still avoid for anything pulled up at reset | p33; DS12288 p21, p71 |
| PG10 | NRST | p23 |

## Resource summary

| Resource | Used by |
|---|---|
| LPUART1 | ST-LINK debug console (PA2 / PA3) |
| USART1 | RC receiver (PC4 / PC5) |
| USART3 | ESP32 telemetry (PB10 / PB11) |
| UART4 | LiDAR (PC10 / PC11) |
| UART5 | Spare UART (PC12 / PD2) |
| USART2 | Free, but its TX options here are all on board-reserved pins |
| TIM3 CH1 / CH2 | ESC (PC6), servo (PC7) |
| TIM4 CH1 | LiDAR motor (PB6) |
| TIM5 | Not used (Mbed OS reserves it for its system tick; avoid if Mbed is used) |
| I2C3 | Expansion I2C (PC8 / PC9) |
| SPI2 | Expansion SPI (PB12–PB15) |
| FDCAN1 | Reserved CAN (PA11 / PA12) |
| ADC1 IN1 / IN2 | Battery sense (PA0), spare analog (PA1) |
| DMA channels | TODO: assign when firmware is written (G4 DMAMUX lets any channel serve any request) |

## Still to check

Step-by-step checklist with clickable boxes:
[issue #2](https://github.com/arionstern/GAR_Vehicle_Controller/issues/2).

- [x] Every AF number above against the DS12288 alternate-function table (pp. 73–76, all match)
- [x] Morpho header pin numbers from UM2505 (pp. 32–33)
- [x] 5 V tolerance for `RC_UART_RX`, `LIDAR_UART_RX`, `TELEM_UART_RX` (see above; decision pending on PC5)
- [x] Nucleo solder bridges: VCP on PA2 / PA3 by default (UM2505 p24–25)
- [ ] LSE crystal X2 actually fitted on our board (look at the board)
- [ ] RPLiDAR A1 motor-control requirement (PWM vs on/off, logic level)

## Change log

| Date | Change | By |
|---|---|---|
| 2026-10-06 | File created, no assignments | — |
| 2026-10-08 | Linked rows to `signals.md`; added reservations for other teams | — |
| 2026-11-04 | First proposed assignment of every signal | — |
| 2026-11-04 | Verified against DS12288 Rev 6 and UM2505 Rev 7; header numbers and 5 V tolerance added | — |
