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

### ER4 facts (from the manual and product page)

Sources: [ER4 user manual (PDF)](https://cdn.shopify.com/s/files/1/0609/8324/7079/files/ER4_User_Manual.pdf?v=1722224113),
[RadioMaster ER4 product page](https://www.radiomasterrc.com/products/er4-2-4ghz-elrs-pwm-receiver).

| Item | Value |
|---|---|
| Supply voltage | DC 4.5–8.4 V (ventilate at 7.4 V and above) |
| Outputs | 4 PWM channels; no dedicated serial port |
| Firmware | ExpressLRS 3.3.0 pre-installed |
| Connector | 2 × 6 pin header, 2.54 mm pitch (15.5 mm wide) |
| Size / weight | 30 × 15 × 5 mm, 3 g |
| Antenna | U.FL connector, 15 cm antenna |
| EXT-V | Solder pad for battery-voltage telemetry, 35 V max. If unused, the receiver reports its own supply voltage |
| Telemetry power | 10 mW |

Header pinout. Each channel has three pins (signal, +, −). CH2 sits directly
under CH1, and CH4 under CH3.

| Position | Top row | Bottom row |
|---|---|---|
| 1 | CH1 signal | CH2 signal |
| 2 | + | + |
| 3 | − | − |
| 4 | − | − |
| 5 | + | + |
| 6 | CH3 signal | CH4 signal |

### How it connects to this board

The receiver is **not** mounted on the PCB. It connects by jumper wires to a
header (J1) on this board, so the receiver's mirrored pin layout does not affect
the footprint, and the receiver can be placed where its antenna has clearance.

Only four of the receiver's twelve pins are wired:

| J1 pin | Net | Receiver pin |
|---|---|---|
| 1 | Serial data line A | A channel signal pin set to serial (TODO: which) |
| 2 | `+5V_LOGIC` | Any + pin |
| 3 | `GND` | Any − pin |
| 4 | Serial data line B | The paired channel signal pin (TODO: which) |

Lines A and B become `RC_UART_RX` / `RC_UART_TX`; which is which depends on the
receiver's pin mapping (TODO). The J1 pin order above is a proposal.

Why two signal wires instead of four: by default each signal pin carries one
PWM channel for one servo. In serial mode the receiver sends every channel as
data on one wire, and a second wire carries data back. The STM32 reads that
stream and drives the servo and ESC itself.

### Configuring the receiver for serial output

One-time setting, stored in the receiver. Steps are from the ExpressLRS
documentation for PWM receivers in general and are **not yet verified on an
ER4**. Sources: [ExpressLRS — PWM receivers](https://www.expresslrs.org/hardware/pwm-receivers/),
[ExpressLRS — receiver serial protocols](https://expresslrs.org/software/serial-protocols/).

1. Power the receiver (5 V) with the transmitter off. After about 60 seconds
   with no radio link the LED blinks rapidly and the receiver starts its own
   Wi-Fi network.
2. Join that network from a phone or laptop (default password is in the ER4 manual).
3. Open `http://10.0.0.1/` in a browser.
4. On the **Model** tab, each output pin has a mode dropdown. Set one pin to
   **Serial TX**; the matching pin switches to **Serial RX** automatically.
   Only pins that support serial offer this option (usually CH2 and CH3).
5. Choose the serial protocol on the same tab: CRSF or SBUS, each with an
   inverted variant.
6. Save and reboot. Record the pins and protocol chosen in the table above.

Notes:
- The T8L transmitter has no screen, so the transmitter-side Lua menu is not
  available; the web page is the only way to change this.
- SBUS output needs ExpressLRS 3.3.0 or newer (the ER4 ships with 3.3.0).
- ExpressLRS uses channel 5 as the arm switch; plan to use it as the
  enable/kill channel.
- With no radio link for 60 seconds the receiver enters Wi-Fi mode and stops
  sending control data. Firmware must treat that as link loss.

### Sheet design requirements

- J1: 4-pin 2.54 mm header (consider 6-pin, bringing all four channel signals
  across, until the serial pins are confirmed)
- Test points on both serial lines, +5 V, and GND
- 0-ohm / solder-jumper options to isolate or reroute each serial line
- Footprint/space for optional SBUS inversion or level conditioning
- Bypass capacitance on the receiver supply at J1

### To verify on a real unit

- [x] ER4 connector layout and supply voltage range (manual)
- [ ] Which channel pins can be set to Serial TX / RX on the ER4
- [ ] That all + pins are common, and all − pins are common (multimeter)
- [ ] Serial logic level (3.3 V expected, not documented)
- [ ] SBUS polarity as output by the ER4, and whether the inverted variant removes the need for a hardware inverter
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
