# Signal List — GAR Vehicle Controller Rev A

Owner: TODO (Task A)

Every signal that crosses between hierarchical sheets is listed here. Subsystem
sheets connect to the rest of the board **only** through these net names, as
hierarchical labels. Which STM32 pin each one lands on is decided later in
`pinmap.md`; you do not need a pin to start drawing.

## Rules

- Add a row here (in a reviewed commit) before using a new inter-sheet net.
- Use the exact name from the table as the hierarchical label, so the same wire
  never gets two names.
- Names are `SUBSYSTEM_FUNCTION`, upper case, underscores.
- UART names are from the **STM32's** point of view: `*_UART_RX` is an MCU
  input, `*_UART_TX` is an MCU output.
- Direction is also from the STM32's point of view.
- Levels are TODO until checked against the module's datasheet.

## Signals to the STM32

| Net name | Direction (MCU) | Peripheral type | Level | Subsystem sheet | Status |
|---|---|---|---|---|---|
| `RC_UART_RX` | in | UART RX (SBUS or CRSF from receiver) | TODO | 03_RC_RECEIVER | Proposed |
| `RC_UART_TX` | out | UART TX (CRSF to receiver) | TODO | 03_RC_RECEIVER | Proposed |
| `TELEM_UART_TX` | out | UART TX (to ESP32) | TODO | 04_TELEMETRY | Proposed |
| `TELEM_UART_RX` | in | UART RX (from ESP32) | TODO | 04_TELEMETRY | Proposed |
| `ESC_PWM` | out | Timer PWM | TODO | 05_ACTUATORS | Proposed |
| `SERVO_PWM` | out | Timer PWM | TODO | 05_ACTUATORS | Proposed |
| `LIDAR_UART_RX` | in | UART RX (from LiDAR) | TODO | 06_SENSORS_EXPANSION | Proposed |
| `LIDAR_UART_TX` | out | UART TX (to LiDAR) | TODO | 06_SENSORS_EXPANSION | Proposed |
| `LIDAR_MOTOR_CTRL` | out | PWM or GPIO (TODO: verify A1 requirement) | TODO | 06_SENSORS_EXPANSION | Proposed |
| `EXP_I2C_SCL` | bidir | I2C | TODO | 06_SENSORS_EXPANSION | Proposed |
| `EXP_I2C_SDA` | bidir | I2C | TODO | 06_SENSORS_EXPANSION | Proposed |
| `EXP_SPI_SCK` | out | SPI | TODO | 06_SENSORS_EXPANSION | Proposed |
| `EXP_SPI_MOSI` | out | SPI | TODO | 06_SENSORS_EXPANSION | Proposed |
| `EXP_SPI_MISO` | in | SPI | TODO | 06_SENSORS_EXPANSION | Proposed |
| `EXP_SPI_CS` | out | GPIO | TODO | 06_SENSORS_EXPANSION | Proposed |
| `EXP_UART_TX` | out | UART TX (spare) | TODO | 06_SENSORS_EXPANSION | Proposed |
| `EXP_UART_RX` | in | UART RX (spare) | TODO | 06_SENSORS_EXPANSION | Proposed |
| `CAN_TX` | out | FDCAN (reserved) | TODO | 06_SENSORS_EXPANSION | Proposed |
| `CAN_RX` | in | FDCAN (reserved) | TODO | 06_SENSORS_EXPANSION | Proposed |
| `VBAT_SENSE` | in | ADC (divided battery voltage) | TODO | 01_POWER | Proposed |
| `EXP_ADC1` | in | ADC (spare) | TODO | 06_SENSORS_EXPANSION | Proposed |

Status values: Proposed (name suggested, not yet agreed) -> Agreed (safe to draw
against) -> Pinned (assigned in `pinmap.md`).

## Power nets

Global power nets, shared by all sheets. Final set depends on the power
architecture decision in `power_budget.md`.

| Net name | Purpose | Status |
|---|---|---|
| `VBAT` | Battery input after protection | Proposed |
| `+5V_ACT` | Actuator supply (servo) | Proposed |
| `+5V_LOGIC` | Logic/module supply | Proposed |
| `+3V3` | 3.3 V, only if needed off-Nucleo | Proposed |
| `GND` | Common return | Proposed |

## Signals owned by other teams

Add rows as other teams confirm what they need from the STM32.

| Net name | Direction (MCU) | Peripheral type | Level | Team | Status |
|---|---|---|---|---|---|
| TODO | | | | BMS (to be confirmed) | Not yet requested |
