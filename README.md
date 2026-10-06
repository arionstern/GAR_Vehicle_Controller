# GAR STM32 Autonomous RC Car

Gator Autonomous Racing's from-scratch autonomous RC car. The primary embedded
controller is an **STM32 Nucleo-G474RE**; there is intentionally no Jetson/SBC
in the core architecture.

## Repository layout

```
GAR_Vehicle_Controller_RevA/   KiCad Rev A carrier/interface PCB
  docs/                        architecture, pin map, power budget, interfaces, bring-up
  libraries/                   project-local symbols, footprints, 3D models
  manufacturing/               generated fab outputs (ignored until a fab release)
  references/                  datasheet and product links
```

## Status

Rev A is a **carrier/interface board** for plug-in modules (Nucleo, RC receiver,
ESP32 dev board, ESC signal, servo). The radio/telemetry budget is not yet
approved and no parts have been physically verified.

**Do not fabricate** until purchased modules, pinouts, voltages, and signal
levels are checked against the footprints (milestone M7).

## Getting started

1. Install **KiCad 10** (files are saved in 10.0 format; older versions cannot open them).
2. Open `GAR_Vehicle_Controller_RevA/GAR_Vehicle_Controller_RevA.kicad_pro`.
   The root schematic is 00_TOP and links one file per subsystem sheet
   (`01_POWER.kicad_sch` ... `06_SENSORS_EXPANSION.kicad_sch`).
3. Put custom symbols in `libraries/symbols/GAR.kicad_sym` and footprints in
   `libraries/footprints/GAR.pretty` (both registered as the `GAR` library in the
   project tables). Any additional library must use a `${KIPRJMOD}/libraries/...`
   path, never a personal/global library path.

## Task delegation

Proposed split — edit the owner if it doesn't fit, and add names as more members
join. Each owner works in the listed branch and opens a pull request for review
before merging. Tick items off as they land on `main`.

| Task | Owner | Branch | Deliverable |
|---|---|---|---|
| A — System architecture / pin map | Arion | `architecture` | `docs/architecture.md`, `docs/pinmap.md` |
| B — Power architecture | Aadesh | `power` | `01_POWER` sheet, `docs/power_budget.md` |
| C — RC receiver (SBUS/CRSF) | Arion | `rc-interface` | `03_RC_RECEIVER` sheet, receiver section of `docs/interface_notes.md` |
| D — Telemetry / ESP32 | Arion | `telemetry` | `04_TELEMETRY` sheet, telemetry section of `docs/interface_notes.md` |
| E — Actuator interfaces | Aadesh | `actuators` | `05_ACTUATORS` sheet |
| F — Nucleo / expansion | Arion | `nucleo-expansion` | `02_STM32_NUCLEO` + `06_SENSORS_EXPANSION` sheets |
| G — PCB layout / mechanical | Aadesh | `layout` | Placement review, then routed `.kicad_pcb` |
| H — Verification / manufacturing | Aadesh | `verification` | Review checklist, `docs/bringup_plan.md`, fab package when approved |

### A — System architecture / pin map (Arion)

Blocks every schematic sheet; do this first.

- [ ] Assign an STM32 peripheral to: receiver UART, ESP32 UART, servo PWM, ESC PWM, LiDAR UART, I2C, SPI, CAN
- [ ] Fill in `docs/pinmap.md` with pin, alternate function, and Nucleo header pin for each
- [ ] List pins already used by the Nucleo board (ST-LINK VCP, SWD, LED, button, oscillators)
- [ ] Decide what is mandatory for Rev A vs deferred
- [ ] Draw the 00_TOP block diagram and hierarchical connections

### B — Power architecture (Aadesh)

- [ ] Fill in the load table in `docs/power_budget.md` from datasheets
- [ ] Choose the battery (chemistry, cell count, connector)
- [ ] Decide servo supply: XR14 BEC, standalone BEC, or on-board regulator
- [ ] Decide how the Nucleo is powered and whether +5V_ACT / +5V_LOGIC are separate
- [ ] Choose polarity protection, fusing, bulk capacitance
- [ ] Draw `01_POWER` with a power LED and test point per rail

### C — RC receiver (Arion)

- [ ] Find the ER4 pad mapping, supply voltage, and logic level
- [ ] Confirm SBUS polarity from the ER4 and STM32G474 UART inversion support
- [ ] Confirm CRSF baud rate and serial configuration
- [ ] Draw `03_RC_RECEIVER`: 4-pin connector, 0-ohm/jumper options, test points, optional inverter footprint
- [ ] Draft SBUS and CRSF firmware interface requirements

### D — Telemetry / ESP32 (Arion)

- [ ] Pick one ESP32 dev-board form factor (or a generic header/cable)
- [ ] Define the STM32-to-ESP32 UART pinout and power requirements
- [ ] Check antenna keep-out and placement constraints
- [ ] Draw `04_TELEMETRY` with UART test points and optional display header
- [ ] Outline a minimal laptop telemetry protocol

### E — Actuator interfaces (Aadesh)

- [ ] Verify the XR14 command interface, signal level, and connector pinout
- [ ] Verify CN0193 voltage, stall current, pulse range, and pin order
- [ ] Draw `05_ACTUATORS`: ESC and servo connectors, PWM nets, test points, servo power path
- [ ] Document failsafe expectations (loss of valid RC command gives neutral throttle)

### F — Nucleo / expansion (Arion)

- [ ] Build and verify the Nucleo-G474RE symbol and header footprint against ST's mechanical drawing
- [ ] Route assigned MCU signals to named hierarchical labels in `02_STM32_NUCLEO`
- [ ] Verify RPLiDAR A1 connector, power, and UART requirements
- [ ] Add spare UART / I2C / SPI / ADC / CAN headers in `06_SENSORS_EXPANSION`

### G — PCB layout / mechanical (Aadesh)

Starts after schematic review (M3).

- [ ] Board outline, mounting holes, connector edge placement, Nucleo socket placement
- [ ] Antenna clearance for the receiver and ESP32
- [ ] Ground strategy agreed with the power owner
- [ ] Placement review, then routing

### H — Verification / manufacturing (Aadesh)

- [ ] Run ERC/DRC and record intentional exceptions in `docs/bringup_plan.md`
- [ ] Check every custom footprint against a mechanical drawing and a 1:1 print
- [ ] Research PCB ordering (fab house, design rules, cost, lead time)
- [ ] Generate BOM, Gerbers, drill, and position files only after design freeze

## Workflow

- One branch per subsystem (`power`, `rc-interface`, `telemetry`, `actuators`, ...).
- One owner per hierarchical sheet; avoid two people editing the same
  `.kicad_sch` / `.kicad_pcb` at once.
- Claim MCU pins in `docs/pinmap.md` before using them in a schematic.
- Schematic review before layout; ERC/DRC clean (or documented exceptions) before freeze.
- Release tags: `revA-schematic-review`, `revA-layout-review`, `revA-fab`.
