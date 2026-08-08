# Bill of materials

Every price and stock status below was read off the live product page on **2026-08-08**. The
previous revision of this file carried Blueprint AI's estimates; its auto-repair loop had
duplicated parts and invented phantom modules, inflating the total to $152.70. That list was
hand-audited down to $41.59 — and then the audit found the one line that mattered was wrong
anyway. See "What changed" below.

## Phase 1: bench demo

| Part | Qty | Price | Source | Note |
|---|---|---|---|---|
| Raspberry Pi 5, 4GB | 1 | $110.00 | PiShop.us | in stock; see "Why a Pi 5" |
| Raspberry Pi Active Cooler (SC1148) | 1 | $10.95 | PiShop.us | in stock |
| Illuminated toggle switch with cover, red | 1 | $3.95 | Adafruit #3218 | 25 in stock; **LED lights at 3V** |
| Half-size breadboard, 400 tie points | 1 | $4.95 | Adafruit #64 | |
| Premium F/F jumpers, 40 × 3" | 1 | $3.95 | Adafruit #794 | |
| Premium F/M jumpers, 20 × 6" | 1 | $1.95 | Adafruit #1954 | |
| Diffused 5mm LED pack, 25 | 1 | $4.95 | Adafruit #4203 | |
| 220Ω resistors, pack of 25 | 1 | $0.75 | Adafruit #2780 | |
| Alligator-clip-to-male leads, 12 | 1 | $7.95 | Adafruit #3255 | see "No crimper" |
| Samsung PRO Endurance 64GB microSDXC | 1 | ~$14.00 | Amazon MB-MJ64KA | **price not verified**; endurance card, see below |
| **Total** | | **~$163.45** | | |

Optional, skip if a suitable one is already on the desk:

| Part | Qty | Price | Note |
|---|---|---|---|
| Official 27W USB-C PSU | 1 | $12.95 | Any 5V/3A USB-C PD charger works; USB ports cap at 600mA, irrelevant headless |

## What changed, and why

**Why a Pi 5 and not the Zero 2 W.** The original list specified a Pi Zero 2 W at $15. Two
things killed it:

1. **Supply.** On 2026-08-08 the Zero 2 W with headers was out of stock at Adafruit ($19.80) and
   SparkFun ($20.70); PiShop listed $20.75 with no stock indicator, and third-party sellers were
   asking $40–56 against a $15 MSRP. The cheap board is not reliably buyable.
2. **The board has a second job.** This Pi is also the always-on host for the agent fleet itself —
   the watchdog, act-listener and scheduled jobs currently die whenever the laptop sleeps, and a
   monitor must not share the failure domain of what it monitors. 512MB does not carry that.

**No crimper.** Adafruit #3218 uses 0.250" quick-connect tabs and does not ship with spade
connectors. Rather than buy a crimping tool for a bench demo, the switch is clipped with
alligator leads; proper spades get crimped later, for the Phase-2 panel.

**220Ω, not 470Ω.** At 3.3V into a red LED (~2.0V forward drop) 220Ω passes ~6mA — well under the
Pi's 16mA-per-pin limit, and brighter than 470Ω. Either works; 220Ω is what the verified pack
contains.

**An endurance microSD, not a generic one.** This box runs 24/7. The PRO Endurance line is built
for continuous write (it is the surveillance-camera card). NVMe via the M.2 HAT+ (~$13 adapter +
~$35 for 256GB) is the clean upgrade if the card ever becomes the problem — not a day-one need.

**Nothing for a display.** No micro-HDMI cable, keyboard or monitor: flash with Raspberry Pi
Imager, preset WiFi + SSH + hostname, and the board is headless from first boot.

**A card reader IS needed.** The build machine is a MacBook Air, which has no SD slot. The card
ships with a full-size SD adapter, which is useless without one. Without a reader the card cannot
be flashed and the Pi never boots — a $7 part gating the entire build.

**The PSU is not optional here.** An earlier revision said to borrow the laptop's USB-C charger.
That is wrong for this build specifically: the whole point of this Pi is that it keeps running
when the laptop is closed and in a bag, so it cannot share the laptop's power supply.

**No soldering iron.** The enclosure's standoffs take self-tapping M2.5 screws directly into
printed PLA rather than heat-set inserts. Inserts thread better and survive far more
reassemblies, but they need a soldering iron. Self-tapped PLA threads start to strip after
roughly 5–10 reassemblies; the Pi is mounted once, so that ceiling is never approached. If this
ever becomes a frequently-reworked assembly, switch back to inserts and buy the iron then.

## Phase 2: cockpit expansion

Order only once Phase 1 works. **The meter row is not settled** — see the constraint below.

| Part | Qty | Est. | Note |
|---|---|---|---|
| DROK 0-3V DC analog panel meter | 2–3 | $12.99 ea | 3V movement so 3.3V PWM sweeps full scale |
| 1kΩ resistor (RC filter) | per meter | $0.05 | |
| 100µF electrolytic capacitor (RC filter) | per meter | $0.15 | |
| 5mm common-cathode RGB LED | 1 | $1.50 | |
| 330Ω resistor (RGB channels) | 3 | $0.15 | |
| Passive buzzer module, 5V | 1 | $2.00 | event chime |
| 3mm acrylic sheet for faceplate | 1 | $8.00 | cut at the UW Bothell makerspace |
| M2.5 × 6 self-tapping screws | 4 | $0.40 | straight into the printed standoffs — see below |
| M3×10 pan-head screws | 4 | $0.40 | |

Phase-2 prices are **estimates and not verified**; re-check at order time.

### The Phase-2 constraint

The 3D design (`hardware/parts/breaker_faceplate.py` in the career-radar repo) reserves the meter
row as a keepout and measures it against the switch. The result: **only 75mm is free beside the
guarded toggle on a 160mm plate.** DROK analog meters are ~45mm bezels, so **three do not fit.**

Phase 2 must pick one: widen the plate to roughly 215mm, drop to two meters, or move the meters
to a second face. The bezel dimension is itself unverified until real meters are in hand, so this
is recorded rather than resolved.

## Open questions before ordering

- **The switch bore diameter is unknown.** Adafruit publishes a 66 × 50 × 20mm overall envelope
  and no panel-bore figure, and guarded toggles ship with 6mm to 12mm bushings. The faceplate
  design therefore exports with that one hole **uncut**, and its fabrication gate refuses while
  the value is unknown. Measure the bushing when the switch arrives.
- Re-verify the microSD price at checkout; it is the one line above that a live page did not confirm.
