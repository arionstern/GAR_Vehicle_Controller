# Interface Notes — GAR Vehicle Controller Rev A

One section per external interface. Record the connector, pinout, electrical
levels, and the source that was checked. Nothing here is verified yet.

## BMS / power distribution board (01_POWER)

Owner: TODO (Task B)

Designed by a separate team in [BMS_GAR](https://github.com/Ryan-Maisuk/BMS_GAR).
Per that repo's README, the board is intended to distribute battery power,
generate 5 V and 3.3 V rails, provide fuse / reverse-polarity / emergency-shutdown
protection, and report battery voltage and current to the vehicle controller.
Its design is at the requirements stage, so nothing below is fixed. Only the
interface between the two boards belongs in this repo.

To agree with the BMS team:
- [ ] Rails delivered to this board, with voltage and current limit for each
- [ ] Power connector type, pinout, and wire gauge
- [ ] Whether the servo and LiDAR are powered from their board directly or through this one
- [ ] Monitoring interface to the STM32: analog (ADC), I2C, or CAN — then reserve it in `pinmap.md` and `signals.md`
- [ ] Emergency-stop behaviour: what cuts power, and whether the STM32 gets a signal
- [ ] Ground connection between the boards

They need from this repo: the load table in `power_budget.md`.

## RC receiver (03_RC_RECEIVER)

Owner: TODO (Task C)

ExpressLRS is the wireless link (T8L to ER4). SBUS or CRSF is the wired link
(ER4 to STM32). Rev A must support both until the team freezes one.

| | SBUS | CRSF |
|---|---|---|
| Direction | Receiver to STM32 | Bidirectional |
| Wires | One data signal | UART TX + RX |
| Firmware | Fixed-frame decode | Framing, packet types, CRC, telemetry |
| Link stats / telemetry back | No | Yes |

Logical connector pins: receiver power, GND, receiver TX/signal -> STM32 RX,
STM32 TX -> receiver RX.

Design requirements:
- Test points on both serial lines
- 0-ohm / solder-jumper options to isolate or reroute each line
- Footprint/space for optional SBUS inversion or level conditioning

To verify:
- [ ] ER4 pad/connector mapping and which pads carry SBUS vs CRSF
- [ ] ER4 supply voltage range and logic level
- [ ] SBUS polarity as output by the ER4, serial parameters
- [ ] CRSF baud rate and serial configuration
- [ ] STM32G474 UART RX/TX inversion and pin-swap support on the chosen UART

Firmware interface requirements (draft): TODO

## Telemetry / ESP32 (04_TELEMETRY)

Owner: TODO (Task D)

- Plug-in ESP32 development board; UART TX/RX + GND + power
- UART test points; optional SPI/I2C access
- Respect antenna keep-out for boards with an onboard PCB antenna

To verify:
- [ ] Chosen dev-board form factor, header pitch and row spacing
- [ ] Supply input pin and voltage, peak current
- [ ] UART pins used on the ESP32 side (avoid boot-strapping pins)
- [ ] Antenna keep-out dimensions

Telemetry protocol (draft): TODO
Candidate values: battery voltage, speed/RPM, steering command, mode, RC link
quality, PID terms, sensor state, fault flags, temperatures, debug counters.

## ESC — Hobbywing XR14 (05_ACTUATORS)

Owner: TODO (Task E)

- Signal connector only; no BLDC phase current on this board
- Test point on the command waveform

To verify:
- [ ] Command interface type, pulse range/frequency, neutral point
- [ ] Signal logic level (3.3 V drive acceptable?)
- [ ] Connector type and pinout
- [ ] Whether the ESC's BEC pin is live on the signal connector, its voltage and current rating

## Servo — SunFounder CN0193 (05_ACTUATORS)

Owner: TODO (Task E)

- 3-pin connector: power, ground, control
- Not powered from the Nucleo regulator
- Bulk capacitance near the connector; test point on the PWM signal

To verify:
- [ ] Operating voltage range and stall current
- [ ] Pulse range/frequency, signal logic level
- [ ] Connector pin order

## LiDAR — RPLiDAR A1 (06_SENSORS_EXPANSION)

Owner: TODO (Task F)

To verify:
- [ ] Connector type and pinout
- [ ] Scanner and motor supply voltages/currents
- [ ] UART baud rate and logic level
- [ ] Motor control input requirement

## Expansion (06_SENSORS_EXPANSION)

Owner: TODO (Task F)

- Spare UART, I2C, SPI, ADC headers, spare GPIO/test pads
- CAN TX/RX or transceiver footprint (populate vs reserve: TODO)
