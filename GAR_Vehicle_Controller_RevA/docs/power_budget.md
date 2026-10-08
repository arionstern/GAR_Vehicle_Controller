# Power Budget — GAR Vehicle Controller Rev A

Owner: TODO (Task B — Power Input Owner)

Do not guess. Every value below needs a datasheet source (link in
`references/datasheet_links.md`) before the 01_POWER schematic is frozen.

## Load table

| Load | Supply voltage (min / nom / max) | Typical current | Peak / stall current | Logic level | Powered from | Source | Verified |
|---|---|---|---|---|---|---|---|
| Nucleo-G474RE | TODO | TODO | TODO | 3.3 V (TODO: confirm 5 V-tolerant pins used) | TODO | TODO | [ ] |
| RadioMaster ER4 receiver | TODO | TODO | TODO | TODO | TODO | TODO | [ ] |
| ESP32 dev board | TODO | TODO | TODO (Wi-Fi TX bursts) | TODO | TODO | TODO | [ ] |
| SunFounder CN0193 servo | TODO | TODO | TODO (stall) | TODO | TODO — not the Nucleo regulator | TODO | [ ] |
| Hobbywing XR14 (control interface) | TODO | TODO | TODO | TODO | TODO | TODO | [ ] |
| Hobbywing XR14 BEC output (as a source) | TODO | — | TODO (rated output) | — | — | TODO | [ ] |
| RPLiDAR A1 (scanner core) | TODO | TODO | TODO | TODO | TODO | TODO | [ ] |
| RPLiDAR A1 (motor) | TODO | TODO | TODO (startup) | TODO | TODO | TODO | [ ] |
| Optional OLED/LCD | TODO | TODO | TODO | TODO | TODO | TODO | [ ] |

## Source — supplied by the BMS / power distribution board

The battery, main protection, 5 V / 3.3 V regulation, and battery monitoring are
expected to come from the BMS team's board
([BMS_GAR](https://github.com/Ryan-Maisuk/BMS_GAR)). **This split is not yet
confirmed with them.** Those items are not designed in this repo; record here
only what that board delivers to this one.

| Item | Value | Confirmed with BMS team |
|---|---|---|
| Rails delivered to this board | TODO | [ ] |
| Voltage and tolerance of each rail | TODO | [ ] |
| Current available per rail | TODO | [ ] |
| Connector and pinout | TODO | [ ] |
| Raw battery voltage range (if VBAT is also delivered) | TODO | [ ] |

## Rail plan (proposed names, to be confirmed)

| Net | Purpose | Source | Loads | Total typical | Total peak |
|---|---|---|---|---|---|
| VBAT | Raw battery, only if delivered to this board | BMS board (TODO) | TODO | TODO | TODO |
| +5V_ACT | Actuator supply (servo) | TODO (BMS board or ESC BEC) | Servo | TODO | TODO |
| +5V_LOGIC | Logic/module supply | BMS board (TODO) | Nucleo, receiver, ESP32, LiDAR (TODO) | TODO | TODO |
| +3V3 | Only if needed off-Nucleo | TODO | TODO | TODO | TODO |
| GND | Common return | — | — | — | — |

## Decisions to make

With the BMS team:
- [ ] Which rails they supply, and their current limits
- [ ] Who powers the servo, and whether it plugs into this board or theirs
- [ ] Who powers the LiDAR
- [ ] Where and how grounds are joined between the two boards

On this board:
- [ ] How the Nucleo is powered (which input pin/jumper setting) and from which rail
- [ ] Whether +5V_ACT and +5V_LOGIC are separate rails
- [ ] Local input protection (fuse, reverse-polarity) beyond what the BMS board provides
- [ ] Bulk capacitance at the servo connector
- [ ] Power LED and test point on each rail
- [ ] Never parallel two regulator outputs (e.g. ESC BEC and a BMS-board 5 V rail on the same net)
