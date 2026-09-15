# Custom MP3 Player

**A portable music player, from blank schematic to finished board**
2026 · Solo project

[← Back to portfolio](../README.md)

---

## Goal

Design a battery-powered MP3 player as a single custom PCB — not an assembly of
breakout boards — and write the firmware to drive it. The point was to own every
layer: pick the parts, justify them against a spec, lay out the board, get it
fabricated, bring it up, and write the UI that makes it a usable object.

## Specification I designed against

| Requirement | Choice | Why |
|---|---|---|
| Play audio files from removable storage | microSD, SPI | Cheap, swappable, no host needed to load music |
| Quality analog output | **UDA1334ATS** I²S DAC | Dedicated stereo DAC; I²S keeps the audio clock out of the MCU's software path, avoiding the jitter you get driving a PWM or R-2R output |
| Battery powered, USB-C rechargeable | **IP5306** | Integrated charger, boost converter, and fuel gauge in one package — one part instead of three, which matters on a hand-soldered board |
| Visual feedback | 128×64 I²C OLED | Two-wire interface leaves SPI free for the SD card |
| Physical controls | 6 tactile switches | Play/pause, skip, volume, menu — usable without looking |
| Headphone output | 3.5 mm jack | Standard, passive |
| Compute | **ESP32-S3** (N8R2, 8 MB flash / 2 MB PSRAM) | Hardware I²S peripheral, enough PSRAM to buffer decoded audio, Wi-Fi headroom for future features |
| Survive handling | **SMF05C** ESD array | The USB and headphone connectors are the two places a user's static discharge actually lands |

## Board

Designed in KiCad. The BOM is 22 unique parts across ~70 placements, all 0805
passives so the board stays hand-solderable.

**Power path:** USB-C → IP5306 (charge + boost) → 5 V rail → 3.3 V logic.
Decoupling is distributed per-pin rather than lumped: 0.1 µF at every supply
pin, with 10–47 µF bulk capacitance staged near the boost inductor and the DAC.
Analog and digital grounds meet at a single point near the DAC to keep the
switching return currents off the audio ground.

**Interfaces:** I²S to the DAC, SPI to the microSD, I²C to the OLED, GPIO to the
buttons — four buses with no contention, which is the reason the OLED went on
I²C instead of sharing SPI with the SD card.

## Firmware

Written for the ESP32-S3:

- Filesystem walk over the SD card to enumerate tracks
- Streaming decode into a PSRAM ring buffer, feeding the I²S peripheral by DMA
  so audio never stalls on file I/O
- Debounced button handling driving a playback state machine
- OLED UI rendering the track list, current song, and a progress bar

## What I learned

**Choose parts that collapse your BOM.** The IP5306 replaced a charger, a boost
converter, and a fuel gauge. On a board I was going to solder by hand, every
part removed was a failure mode removed.

**I²S exists for a reason.** My first instinct was to generate audio from the
MCU directly. Handing the audio clock to a dedicated DAC over I²S moves the
timing requirement out of software entirely — the MCU can be late delivering a
buffer without that becoming audible distortion.

**Bus allocation is a layout decision, not just a software one.** Deciding the
OLED would be I²C rather than SPI was what let the SD card have SPI to itself,
and it changed the routing.

> *Note: my resume lists this as an "Analog-to-Digital Converter." The UDA1334ATS
> is a **digital-to-analog** converter — the board plays audio out rather than
> recording it in.*

## Stack

ESP32-S3-DEVKITC-1-N8R2 · UDA1334ATS I²S DAC · IP5306 battery management ·
USB-C · microSD · 128×64 I²C OLED · KiCad · C/C++ (ESP-IDF) · SMD soldering
