# The Breaker

**A physical dead-man kill-switch for an autonomous AI agent fleet.**

I run a small fleet of AI agents on my Mac that handle research, monitoring, and operational busywork. They work while I sleep. Software has an off switch only as long as the software cooperates, so I designed The Breaker: a desk panel where a guarded missile switch decides whether the agents are allowed to act at all.

The agents are never *told* to stop. They quietly lose the ability to prove they are still allowed to run. **The default state is stop.**

> **Status:** design of record complete and audited. Enclosure and faceplate now exist as parametric CAD with measured geometry checks; Phase 1 BOM re-priced against live vendor pages on 2026-08-08 (~$163). **Parts not yet ordered.** The authority-token service and agent-side gate are the next build. This repo is the hardware design; the working build and demo video follow.

🔗 [Follow the build on LinkedIn](https://www.linkedin.com/in/tarang-tj) · [More projects](https://github.com/tarang-tj) · [Live design page](https://tarang-tj.github.io/the-breaker/)

---

## How it works

A guarded toggle on the desk feeds a Raspberry Pi 5 (a node on my private Tailscale network). The Pi serves a short-TTL "authority token" over HTTP on the tailnet. Every agent must fetch a fresh token before every action. Let the token lapse and the agent simply can't act.

The board was a Pi Zero 2 W in the original design. It changed for two reasons: the Zero 2 W is not reliably buyable (out of stock at two major US suppliers, with third-party sellers asking 3x MSRP), and this Pi has a second job — it is also the always-on host for the agent fleet, whose watchdog currently dies with the laptop it monitors.

![System architecture](design/system-architecture.svg)

The safe state is **HALT everywhere** — it is reached by *loss*, not by a command, so anything that breaks the chain fails safe.

| Condition | Result |
|---|---|
| Switch OPEN | **HALT** |
| Pi process dead (no token to fetch) | **HALT** |
| Network partition (fetch fails) | **HALT** |
| Token expired / clock skew | **HALT** |
| Switch CLOSED + fresh token | ACT |

This is the dead-man principle from rail and heavy machinery, applied to software agents: authority is something the system must continuously *prove it still holds*, not something you have to remember to revoke.

## Wiring

Solderless, two-wire safety core. `GPIO26` to ground with the Pi's internal pull-up in software; an open or broken circuit reads HIGH = HALT. 5V never touches a GPIO pin.

![Wiring diagram](design/wiring-diagram.svg)

See [`hardware/gpio-map.md`](hardware/gpio-map.md) for the full pin map.

## Faceplate

3mm laser-cut acrylic, **160x120mm**, cut at the UW Bothell makerspace, seating in a printed enclosure on a 3.65mm lip. Phase 2 adds analog gauges reading live agent-activity metrics, an RGB status LED, and an event chime.

The plate grew from 150x110 because the parametric model measures rather than assumes: the guarded toggle's cover sweeps a 66x50mm envelope, and at 150x110 it left 5mm of acrylic to the edge against a 6mm minimum. **The switch sizes this panel — the Pi does not.**

Two things the geometry checks refused, recorded rather than papered over:

- **Three analog meters do not fit.** Only 75mm is free beside the guard; DROK bezels are ~45mm. Phase 2 must widen the plate to roughly 215mm, drop to two meters, or move them to a second face.
- **The switch bore is not cut.** Adafruit publishes the cover envelope but no panel-bore diameter, and guarded toggles ship with 6–12mm bushings. The plate exports with that one hole absent and the fabrication gate refuses while the value is unknown. A laser cut is one-shot; a guessed hole is a wasted sheet.

![Faceplate layout](design/faceplate-layout.svg)

## Bill of materials

Phase 1 gets a working bench demo for **~$163**, every line but one read off a live vendor page on 2026-08-08. Full itemized list, with sources and the one unverified price, in [`hardware/bom.md`](hardware/bom.md).

| Phase 1 core | Price | Source |
|---|---|---|
| Raspberry Pi 5, 4GB | $110.00 | PiShop.us |
| Raspberry Pi Active Cooler | $10.95 | PiShop.us |
| Illuminated toggle switch with cover | $3.95 | Adafruit #3218 |
| Half-size breadboard, 400 points | $4.95 | Adafruit #64 |
| F/F + F/M jumper wires | $5.90 | Adafruit #794, #1954 |
| 5mm LED pack + 220Ω resistors | $5.70 | Adafruit #4203, #2780 |
| Alligator-clip-to-male leads | $7.95 | Adafruit #3255 |
| Samsung PRO Endurance 64GB microSD | ~$14.00 | *price unverified* |
| **Total** | **~$163.45** | |

## Design deck

The full nine-slide design reveal is in [`design/the-breaker-design-reveal.pdf`](design/the-breaker-design-reveal.pdf), and the individual slides are in [`slides/`](slides/).

## Why I built it

Agent oversight is moving from a research talking point to an engineering problem: if you let software act on your behalf, you want a control you can reason about physically. The Breaker is my take on that — a fail-safe you can see, flip, and trust, designed so that every failure mode I could think of lands on HALT.

---

*Design generated with help from a hardware draft tool, then hand-audited end to end (the auto-generated BOM had duplicate and phantom parts; this list is the corrected one). Renders and diagrams are my own.*
