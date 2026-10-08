# GAR STM32 Autonomous RC Car

Gator Autonomous Racing's from-scratch autonomous RC car. The primary embedded
controller is an **STM32 Nucleo-G474RE**; there is intentionally no Jetson/SBC
in the core architecture.

## Repository layout

```
GAR_Vehicle_Controller_RevA/   KiCad Rev A carrier/interface PCB
  docs/                        architecture, signal list, pin map, power budget, interfaces, bring-up
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

## Order of work

The task letters below are labels, not a sequence. Three orderings are firm:

1. **Signal names before schematics.** A subsystem sheet connects to the rest of
   the board only through net names listed in `docs/signals.md`. Add or agree a
   name there before using it. MCU pins do **not** need to be chosen yet.
2. **Pins before the Nucleo sheet and before layout.** `02_STM32_NUCLEO` is the
   only sheet that ties net names to physical pins. Every pin it uses must be
   claimed in `docs/pinmap.md` first, and the pin map must be complete before
   schematic review (M3).
3. **Reviewed schematic before layout.** Task G starts after schematic review (M3).
4. **Fabrication outputs last.** Nothing is generated for ordering, or ordered,
   until purchased parts are physically checked against the footprints (M7).

Suggested phases:

| Phase | What happens | Tasks involved | Can run in parallel? |
|---|---|---|---|
| 1 — Research | Datasheet verification for every module; record results in `docs/` and `references/datasheet_links.md` | B, C, D, E, F (research items) | Yes, all at once |
| 2 — Signal list | Agree net names, directions, and levels in `docs/signals.md`; decide Rev A scope | A, with every sheet owner | Overlaps with phase 1 |
| 3 — Subsystem sheets | Draw sheets `01`, `03`–`06` up to their named hierarchical labels | B, C, D, E, F (drawing items) | Yes, one owner per sheet |
| 4 — Pin assignment | Fill in `docs/pinmap.md`, then wire `02_STM32_NUCLEO` and the 00_TOP connections | A, F | No — single owner for the pin map |
| 5 — Schematic review | ERC, peer review, resolve pin/resource conflicts | H, everyone | — |
| 6 — Layout | Placement review, then routing | G | No — one person in the `.kicad_pcb` at a time |
| 7 — Verification | DRC, footprint 1:1 prints, physical fit check, then fab package | H | — |

Notes:
- Until phase 4, ERC will report the hierarchical labels on 00_TOP as
  unconnected. That is expected.
- Where a sheet's circuit depends on the pin eventually chosen (SBUS inversion,
  5 V signals into the MCU, analog inputs), draw it as an optional footprint or
  jumper rather than waiting.
- Other teams sharing the STM32 (for example BMS) reserve their pins in
  `docs/pinmap.md`, not in a separate file.
- Settle the battery and servo-supply decisions (Task B) early; the actuator
  sheet (Task E) depends on them.
- The Nucleo footprint (Task F) and PCB-ordering research (Task H) have no
  dependencies and can be done at any time.

## Tasks

Owners are not assigned yet. To take a task, put your name in the Owner column
in a commit to `main`. Each owner works in the listed branch and opens a pull
request for review before merging. Tick items off as they land on `main`.

| Task | Owner | Branch | Deliverable |
|---|---|---|---|
| A — System architecture / pin map | _unassigned_ | `architecture` | `docs/architecture.md`, `docs/signals.md`, `docs/pinmap.md` |
| B — Power architecture | _unassigned_ | `power` | `01_POWER` sheet, `docs/power_budget.md` |
| C — RC receiver (SBUS/CRSF) | _unassigned_ | `rc-interface` | `03_RC_RECEIVER` sheet, receiver section of `docs/interface_notes.md` |
| D — Telemetry / ESP32 | _unassigned_ | `telemetry` | `04_TELEMETRY` sheet, telemetry section of `docs/interface_notes.md` |
| E — Actuator interfaces | _unassigned_ | `actuators` | `05_ACTUATORS` sheet |
| F — Nucleo / expansion | _unassigned_ | `nucleo-expansion` | `02_STM32_NUCLEO` + `06_SENSORS_EXPANSION` sheets |
| G — PCB layout / mechanical | _unassigned_ | `layout` | Placement review, then routed `.kicad_pcb` |
| H — Verification / manufacturing | _unassigned_ | `verification` | Review checklist, `docs/bringup_plan.md`, fab package when approved |

### A — System architecture / pin map
Early (unblocks the subsystem sheets):

- [ ] Review and publish the net names in `docs/signals.md` with every sheet owner
- [ ] Decide what is mandatory for Rev A vs deferred
- [ ] Collect pin reservations from other teams (for example BMS) in `docs/pinmap.md`

Later (before schematic review):

- [ ] List pins already used by the Nucleo board (ST-LINK VCP, SWD, LED, button, oscillators)
- [ ] Assign an STM32 peripheral and pin to every signal in `docs/signals.md`
- [ ] Fill in `docs/pinmap.md` with pin, alternate function, and Nucleo header pin for each
- [ ] Draw the 00_TOP block diagram and hierarchical connections

### B — Power architecture
- [ ] Fill in the load table in `docs/power_budget.md` from datasheets
- [ ] Choose the battery (chemistry, cell count, connector)
- [ ] Decide servo supply: XR14 BEC, standalone BEC, or on-board regulator
- [ ] Decide how the Nucleo is powered and whether +5V_ACT / +5V_LOGIC are separate
- [ ] Choose polarity protection, fusing, bulk capacitance
- [ ] Draw `01_POWER` with a power LED and test point per rail

### C — RC receiver
- [ ] Find the ER4 pad mapping, supply voltage, and logic level
- [ ] Confirm SBUS polarity from the ER4 and STM32G474 UART inversion support
- [ ] Confirm CRSF baud rate and serial configuration
- [ ] Draw `03_RC_RECEIVER`: 4-pin connector, 0-ohm/jumper options, test points, optional inverter footprint
- [ ] Draft SBUS and CRSF firmware interface requirements

### D — Telemetry / ESP32
- [ ] Pick one ESP32 dev-board form factor (or a generic header/cable)
- [ ] Define the STM32-to-ESP32 UART pinout and power requirements
- [ ] Check antenna keep-out and placement constraints
- [ ] Draw `04_TELEMETRY` with UART test points and optional display header
- [ ] Outline a minimal laptop telemetry protocol

### E — Actuator interfaces
- [ ] Verify the XR14 command interface, signal level, and connector pinout
- [ ] Verify CN0193 voltage, stall current, pulse range, and pin order
- [ ] Draw `05_ACTUATORS`: ESC and servo connectors, PWM nets, test points, servo power path
- [ ] Document failsafe expectations (loss of valid RC command gives neutral throttle)

### F — Nucleo / expansion
- [ ] Build and verify the Nucleo-G474RE symbol and header footprint against ST's mechanical drawing
- [ ] Route assigned MCU signals to named hierarchical labels in `02_STM32_NUCLEO`
- [ ] Verify RPLiDAR A1 connector, power, and UART requirements
- [ ] Add spare UART / I2C / SPI / ADC / CAN headers in `06_SENSORS_EXPANSION`

### G — PCB layout / mechanical
Starts after schematic review (M3).

- [ ] Board outline, mounting holes, connector edge placement, Nucleo socket placement
- [ ] Antenna clearance for the receiver and ESP32
- [ ] Ground strategy agreed with the power owner
- [ ] Placement review, then routing

### H — Verification / manufacturing
- [ ] Run ERC/DRC and record intentional exceptions in `docs/bringup_plan.md`
- [ ] Check every custom footprint against a mechanical drawing and a 1:1 print
- [ ] Research PCB ordering (fab house, design rules, cost, lead time)
- [ ] Generate BOM, Gerbers, drill, and position files only after design freeze

## Workflow

- One branch per subsystem (`power`, `rc-interface`, `telemetry`, `actuators`, ...).
- One owner per hierarchical sheet; avoid two people editing the same
  `.kicad_sch` / `.kicad_pcb` at once.
- Add inter-sheet net names to `docs/signals.md` before using them; claim MCU
  pins in `docs/pinmap.md` before wiring them on the Nucleo sheet.
- Schematic review before layout; ERC/DRC clean (or documented exceptions) before freeze.
- Release tags: `revA-schematic-review`, `revA-layout-review`, `revA-fab`.
