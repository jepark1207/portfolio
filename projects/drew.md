# D.R.E.W. — Desk Roaming Exploration Widget

**A small autonomous robot on a PCB designed from primary datasheets**
2026 · In progress

[← Back to portfolio](../README.md)

---

## What it is

A robot that explores a desk surface without hitting obstacles or driving off
the edge.

- **Obstacle avoidance** — ultrasonic rangefinder on a pan servo, sweeping to
  find clear headings
- **Edge detection** — downward-facing time-of-flight sensor, where the desk
  edge reads as a sudden out-of-range return
- **Brain** — STM32G0B1CBT6 soldered directly to a custom PCB, flashed and
  powered over USB-C

## Why I'm building it

This one is explicitly a *learning* project, and the deliverable is two specific
skills rather than just a working robot:

1. **PCB design** — schematic capture, power integrity, decoupling strategy,
   ground planes, design rules, fabrication output
2. **Reading datasheets to drive design decisions** — deriving pin constraints,
   absolute maximums, and reference circuits from primary documentation rather
   than copying a reference design or a tutorial

That second goal is the real one. On earlier boards I leaned on reference
designs; here every decision has to trace to a page in a datasheet. The
repository documentation records *why* each decision was made, not just what was
decided.

## Hardware

| Subsystem | Part | Integration |
|---|---|---|
| MCU | **STM32G0B1CBT6** (LQFP48) | On-board |
| Battery management | IP5306-I²C | On-board |
| Logic rail | 5 V → 3.3 V regulator | On-board |
| Motor driver | HW-627 (DRV8833) | Breakout, header |
| Obstacle sensor | HC-SR04 | Breakout, header |
| Edge sensor | VL53L1X | Breakout, header |
| Pan actuator | Micro servo | Header |
| Drive | 2× N20-class gearmotors | Wired to HW-627 |

**v1 is a "brain board."** The PCB carries the MCU, power, USB-C, and
connectors; sensors and the H-bridge stay as breakout modules. This was a
deliberate scoping call — it keeps the number of unknowns on a first board low
and keeps it debuggable. Integrating them onto the PCB is the v2 exercise.

## Design decisions worth noting

**The MCU changed from ESP32-S3 to STM32G0B1.** The ESP32-S3 was the familiar
choice, having used it on the [MP3 player](custom-mp3.md). But this robot needs
no wireless, and the STM32G0 is cheaper, lower power, and — more to the point —
has documentation detailed enough to support the datasheet-driven design that is
the whole objective. Choosing the part that made the *learning* goal achievable
over the part I already knew was the right trade.

**5 V tolerance decides a level shifter.** The HC-SR04's `ECHO` pin drives 5 V.
Whether that needs a level shifter depends entirely on which STM32G0 pins are
`FT` (5 V tolerant) — a question answerable only from the datasheet's pin table,
and one that changes the schematic.

**ESP-style convenience isn't free on a bare MCU.** Confirming that USB DFU is
supported by this part's ROM bootloader determines whether the board can be
flashed over USB-C at all, or whether it needs an SWD header — a footprint
decision driven by a single line of documentation.

## Documentation

The repository carries nine design documents: a decision log recording every
choice and its alternatives, architecture and block diagram, power budget,
datasheet-derived pin assignment, BOM with verified part numbers, layout rules
and stackup, the verified power section, and a block-by-block build guide.

## Status

**Design phase — nothing fabricated yet.** Architecture and the power section
are settled and verified. Schematic capture is gated on a set of open datasheet
questions, tracked explicitly in the repo's datasheet worksheet: the alternate
function mapping table, which pins are 5 V tolerant, ROM bootloader USB DFU
support, the IP5306 charge-current register, and the VL53L1X ranging status
codes that *are* the edge detection logic.

## Stack

STM32G0B1CBT6 · KiCad 10 · STM32CubeIDE · IP5306 · VL53L1X · DRV8833 · HC-SR04 ·
JLCPCB PCBA
