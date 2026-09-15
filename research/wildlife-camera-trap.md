# Low-Power Wildlife Camera Trap

**Stanford Smart Sensing Systems Lab** · 2026
Advisor: **Dr. Zerina Kapetanovic**

[← Back to portfolio](../README.md)

---

## The project

A wildlife camera trap that captures and classifies infrared images in the
field, on a power budget small enough to run unattended. Camera traps are only
useful if they survive deployment without intervention, which makes power and
reliability the actual engineering problem — not image quality.

I work on the IR illumination hardware, the embedded capture software, the
mechanical enclosure, and the transition from prototype to a fabricated PCB.
The board is a team effort across nine modules (power, MCU power, MCU I/O,
camera, PIR, IR flash, temperature, PSRAM, LoRa); **IR flash is mine.**

## What I own

### IR flash driver hardware

Designed and specified the LED driver. The IR LEDs draw far more current than a
Raspberry Pi GPIO pin can source, so the pin can only act as a *control* signal,
never the current path:

- Selected **MOSFET switching components** to carry the LED current, gated by
  GPIO
- Calculated **current-limiting resistor values** against Raspberry Pi GPIO
  specifications — sizing for LED forward voltage and target current while
  keeping the GPIO within its absolute maximum
- Added **ESD protection** for field deployment, where the board is handled
  outdoors and connectors are exposed

### Embedded control software

Python control software integrating camera capture with the GPIO-triggered IR
flash. The ordering constraint is the interesting part: the flash must be
asserted and settled *before* the shutter, and released after — get the sequence
wrong and you get a correctly-exposed photograph of nothing, or an LED duty
cycle that wastes the power budget.

### Field deployment trigger

Integrated a **VL53L0X time-of-flight sensor** to trigger capture on animal
presence, and configured a **systemd service** for auto-start on boot. The
systemd piece matters more than it sounds: a trap deployed in the field has no
one to log in and start a script, so the capture pipeline has to come up
unattended after any power cycle.

### Mechanical

- Fabricated a custom **protoboard layout** to mount the camera and drive
  circuitry
- **Laser-cut** a plywood mount
- **3D printed** an enclosure in CAD to secure and position all components

Positioning is a real constraint here, not cosmetic: the IR LEDs have to
illuminate the camera's field of view without shining into the lens, and the ToF
sensor has to point where an animal will actually be.

### Prototype → PCB

Created a custom PCB from the working prototype, finalized the power and thermal
design, and added ESD protection for field deployment.

## Bring-up problems I diagnosed and resolved

Hardware bring-up is where most of the real work happened:

| Fault | Nature of the problem |
|---|---|
| **Camera overlay failures** | Capture pipeline failing at the display/buffer layer rather than the sensor |
| **ST-LINK firmware errors** | Programmer/target communication — the class of fault where nothing works and nothing indicates why |
| **Relay voltage error** | A voltage mismatch on the switching path, found by measurement rather than inspection |

These are the faults that don't appear in a schematic review. Each needed
bisecting a system that had several plausible culprits down to one — the skill
that transfers.

## Stack

Raspberry Pi · Python · KiCad · MOSFET LED driver design · VL53L0X ToF ·
systemd · ST-LINK · CAD / 3D printing · laser cutting · protoboard fabrication ·
oscilloscope and multimeter debugging
