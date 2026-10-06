# Bring-Up Plan — GAR Vehicle Controller Rev A

Owner: TODO (Task H — Verification / Manufacturing Owner)

Bring the board up one subsystem at a time. **Do not connect the motor on the
first power-up.** Record measured values and the date next to each step.

## Pre-fabrication checks

- [ ] ERC clean or exceptions documented
- [ ] DRC clean or exceptions documented
- [ ] Every custom footprint checked against a mechanical drawing and a 1:1 print
- [ ] Purchased modules physically compared to footprints and pinouts (M7)

## Power-on sequence

| # | Step | Expected | Measured | Pass |
|---|---|---|---|---|
| 1 | Continuity check VBAT / 5 V / 3.3 V / GND for shorts (unpowered, no modules) | No shorts | | [ ] |
| 2 | Power from a current-limited bench supply (limit: TODO) | No excess current | | [ ] |
| 3 | Verify each rail at its test point | TODO per rail | | [ ] |
| 4 | Install Nucleo only after carrier power is verified | Boots, ST-LINK connects | | [ ] |
| 5 | UART loopback / basic comms on unused headers | Data echoes | | [ ] |
| 6 | Install receiver; inspect SBUS/CRSF with logic analyzer or scope | Valid frames | | [ ] |
| 7 | Decode steering/throttle channels with no actuators connected | Values track sticks | | [ ] |
| 8 | Connect servo, steering unloaded; verify neutral and limits | Within capped range | | [ ] |
| 9 | Connect ESC, motor mechanically secured, conservative limits | Controlled response | | [ ] |
| 10 | Implement and test timeout/failsafe before any free driving | Neutral on link loss | | [ ] |
| 11 | Add ESP32 telemetry once the control path is stable | Data on laptop | | [ ] |

## Failsafe tests

- [ ] Transmitter off while throttle applied -> neutral within timeout (TODO: value)
- [ ] Receiver unplugged -> neutral
- [ ] MCU reset -> outputs safe during and after boot
- [ ] Enable/kill channel off -> no motor actuation

## Intentional ERC/DRC exceptions

| Rule | Location | Reason | Approved by |
|---|---|---|---|
| | | | |
