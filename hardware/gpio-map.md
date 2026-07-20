# GPIO map

Canonical pin assignments, resolved across design revisions. Raspberry Pi Zero 2 W, BCM numbering.

| Function | Pin | Wiring | Notes |
|---|---|---|---|
| Halt switch | GPIO26 (phys 37) → GND (phys 39) | internal 3.3V pull-up in software, no external resistor | open circuit reads HIGH = HALT. Never route 5V to a GPIO. |
| Status LED | GPIO21 | GPIO21 → 470Ω → LED → GND | green = armed, amber = HALT |
| Relay (optional) | GPIO20 | GPIO20 → IN, module VCC from 5V rail, GND common | cosmetic click only |
| Phase 2 meters | GPIO12 / GPIO13 / GPIO18 | each GPIO → 1kΩ → node(100µF to GND) → meter V+ | RC-filtered PWM. DROK 0-3V movements: 3.3V PWM sweeps full scale. |
| Phase 2 RGB LED | 3 channels | one 330Ω per channel | assign final pins in code, not from any generated schematic |
| Phase 2 chime | any free GPIO | passive buzzer module: GPIO + 5V VCC + GND | event chime |

## Safety rules

- The two-wire halt circuit is the whole safety story: `GPIO26` to `GND`, nothing else. A broken wire, a dead Pi, or an open switch all read HALT.
- 5V never touches a GPIO pin. Verified against the netlist: no 5V-to-GPIO nets exist.
- The meters are driven by RC-filtered PWM, never from the 3.3V rail through a voltage divider.
