# Architecture — GAR Vehicle Controller Rev A

Owner: TODO (Task A — System Architecture / Interface Owner)

## Principles

- The STM32 Nucleo-G474RE is the real-time vehicle controller and the final
  authority for actuator commands.
- The ESP32 is a telemetry/networking peripheral only.
- Rev A is a carrier/interface board for plug-in modules. No bare STM32, no
  bare ESP32, no three-phase power stage.
- Anything not verified against a datasheet or the physical part is marked
  `TODO / NEEDS DATASHEET VERIFICATION`.

## Signal paths

```
Manual:      T8L transmitter --ELRS 2.4 GHz--> ER4 receiver --SBUS or CRSF--> STM32 --> ESC + servo
Telemetry:   STM32 --UART--> ESP32 dev board --Wi-Fi--> laptop / dashboard
Autonomous:  LiDAR + IMU + sensors --> STM32 --> control logic --> ESC + servo
```

## Modules

| Module | Part | Role | Status |
|---|---|---|---|
| Controller | STM32 Nucleo-G474RE | Vehicle controller (plug-in) | On hand |
| ESC | Hobbywing Xerun XR14 | Commercial ESC, commanded by STM32 | On hand |
| Motor | Hobbywing Xerun 2848 sensored BLDC, 2800KV | Traction | On hand |
| Steering | SunFounder CN0193 servo | Steering | On hand |
| LiDAR | RPLiDAR A1 | Perception | On hand |
| RC transmitter | RadioMaster T8L (2.4 GHz ELRS) | Manual control | Candidate, not purchased |
| RC receiver | RadioMaster ER4 (2.4 GHz ELRS) | SBUS/CRSF to STM32 | Candidate, not purchased |
| Telemetry | ESP32 development board | UART-to-Wi-Fi bridge | Candidate, form factor TODO |
| Battery + BMS / power distribution | Separate board, [BMS_GAR](https://github.com/Ryan-Maisuk/BMS_GAR) | Battery, protection, regulated rails, battery monitoring | Other team; interface not yet agreed |

## Hierarchical schematic sheets

| Sheet | Purpose | Owner |
|---|---|---|
| 00_TOP | Top-level block diagram and hierarchical connections | TODO |
| 01_POWER | Power input from the BMS board, local protection, rail test points | TODO |
| 02_STM32_NUCLEO | Nucleo headers, MCU pin assignments, debug/expansion | TODO |
| 03_RC_RECEIVER | ER4 connector, SBUS/CRSF routing, jumpers/conditioning | TODO |
| 04_TELEMETRY | ESP32 dev-board header, UART, optional display header | TODO |
| 05_ACTUATORS | XR14 signal connector, servo connector, actuator power | TODO |
| 06_SENSORS_EXPANSION | LiDAR, I2C/SPI/UART/CAN expansion headers | TODO |

## Rev A scope

In scope:
- Nucleo plug-in headers
- Generic 4-pin RC receiver connector supporting both SBUS and CRSF
- ESP32 dev-board header (UART + power)
- XR14 ESC signal connector, 3-pin servo connector
- Power input, protection, regulation, rail test points
- Expansion headers and test points

Out of scope (later revisions):
- Custom ESC / gate driver / MOSFET bridge / phase-current sensing
- Bare MCU or bare radio module integration
- Unverified sensors (e.g. IMU footprint)

## Safety / failsafe requirements

- Loss of valid receiver frames for a defined timeout (TODO: value) commands neutral throttle.
- A manual enable/kill channel is required before motor actuation.
- Outputs default to safe states on boot/reset.
- Steering and throttle ranges are capped during bench bring-up.
- A physical power/kill strategy is planned as the platform matures; a software E-stop alone is not sufficient.

## Open questions (must be resolved before fabrication)

- [ ] ER4 pin/pad mapping for SBUS and CRSF configurations
- [ ] Whether the chosen SBUS mode is inverted, and whether the selected STM32 UART can invert directly
- [ ] Receiver logic level and supply voltage
- [ ] XR14 control signal expectations and connector pinout
- [ ] CN0193 operating voltage and realistic stall current
- [ ] Power interface with the BMS board: rails, connector, current limits, monitoring link
- [ ] ESP32 form factor: specific DevKit footprint vs generic header/cable
- [ ] Nucleo-G474RE mechanical/header footprint dimensions
- [ ] RPLiDAR A1 power and serial connection on this revision
- [ ] CAN transceiver: populate on Rev A or reserve footprint only

## Milestones

| Milestone | Exit criteria | Done |
|---|---|---|
| M0 Architecture freeze | Block diagram, interfaces, owner assignments, initial pin map | [ ] |
| M1 Datasheet verification | All module voltages, connectors, signal levels documented | [ ] |
| M2 Schematic alpha | All hierarchical sheets connected; no layout | [ ] |
| M3 Schematic review | ERC, peer review, pin/resource conflicts resolved | [ ] |
| M4 Placement review | Mechanical placement, antenna clearance, connectors, power flow | [ ] |
| M5 Routing beta | Routed with test points, planes, constraints | [ ] |
| M6 DRC + fabrication hold | Fab files can be generated; do not order | [ ] |
| M7 Physical fit verification | Purchased parts compared to footprints and pinouts | [ ] |
| M8 Fabrication approval | Leadership/budget approval and final sign-off | [ ] |
