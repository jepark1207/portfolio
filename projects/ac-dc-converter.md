# Analog AC-DC Converter

**Wall AC to regulated 5 V DC, built stage by stage from discrete components**
2026 · Stanford Circuits 1

[← Back to portfolio](../README.md)

---

## Goal

Build a complete AC-to-DC power supply from discrete parts and hit a **5 V DC**
target — no integrated regulator module, nothing bought pre-assembled. Each
stage was added on top of the last, measured on an oscilloscope, and only then
built upon.

## The four stages

| Stage | Circuit | What it fixes | What's still wrong after it |
|---|---|---|---|
| **1. Rectifier** | Diode bridge | AC has no DC average — the bridge folds the negative half-cycles positive | Output is a pulsating 120 Hz waveform, not DC |
| **2. Filter** | Smoothing capacitor | Holds charge between peaks, converting pulses toward a DC level | Residual ripple; sags under load |
| **3. Regulator** | Zener reference | Pins the output to a fixed voltage independent of ripple and input swing | Can only supply a few mA before the reference collapses |
| **4. Source follower** | MOSFET | Buffers the reference so real load current comes from the supply rather than the regulator | — |

Each stage exists because of a specific, measurable defect in the one before it.
That was the actual lesson of the project: a power supply isn't four blocks
someone chose arbitrarily, it's four answers to four problems that appear in
sequence, and you can watch each one on the scope.

## What I took from it

**The source follower is the non-obvious stage.** A Zener will set a voltage but
cannot source meaningful current — draw from it directly and the output droops.
Putting a MOSFET between the reference and the load decouples *setting* the
voltage from *supplying* the current. This is the idea that reappeared later in
every regulator I've specified on a PCB.

**Ripple is a design parameter, not a defect.** Filter capacitor sizing is a
trade between ripple amplitude and inrush current. There's no value that makes
both small, so you pick based on which one the downstream stage cares about.

**Measure every stage before adding the next.** Building all four and debugging
at the end would have made it impossible to attribute a fault to a stage. Doing
it incrementally meant every problem had exactly one candidate cause.

## Stack

Discrete diodes, capacitors, Zener reference, power MOSFET · breadboarding ·
oscilloscope · waveform generator · bench multimeter
