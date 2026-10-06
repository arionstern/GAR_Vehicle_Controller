# Power Budget — GAR Vehicle Controller Rev A

Owner: TODO (Task B — Power Architecture Owner)

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

## Source

| Item | Value | Verified |
|---|---|---|
| Battery chemistry / cell count | TODO | [ ] |
| Battery voltage range (empty – full) | TODO | [ ] |
| Battery connector | TODO | [ ] |

## Rail plan (proposed names, to be confirmed)

| Net | Purpose | Source | Loads | Total typical | Total peak |
|---|---|---|---|---|---|
| VBAT | Raw battery input after protection | Battery | TODO | TODO | TODO |
| +5V_ACT | Actuator supply (servo) | TODO (BEC or dedicated regulator) | Servo | TODO | TODO |
| +5V_LOGIC | Logic/module supply | TODO | Nucleo, receiver, ESP32, LiDAR (TODO) | TODO | TODO |
| +3V3 | Only if needed off-Nucleo | TODO | TODO | TODO | TODO |
| GND | Common return | — | — | — | — |

## Decisions to make

- [ ] Servo powered from XR14 BEC, a standalone BEC, or an on-board regulator
- [ ] How the Nucleo is powered (which input pin/jumper setting) and from which rail
- [ ] Whether +5V_ACT and +5V_LOGIC are separate rails
- [ ] Where and how grounds are joined (coordinate with layout owner)
- [ ] Polarity protection method
- [ ] Fuse / resettable protection strategy and ratings
- [ ] Bulk capacitance at the servo connector
- [ ] Power LED and test point on each rail
- [ ] Never parallel two regulator outputs (e.g. ESC BEC and an on-board 5 V regulator on the same net)
