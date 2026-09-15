# ShadowHive

**Covert mapping, positioning, and communication in foreign environments**
2026 · Three-person hardware/ML system

[← Back to portfolio](../README.md)

---

## The problem

A team entering an unfamiliar building has no floor plan, no reliable way to
track each other through walls, and no way to communicate without being heard.
ShadowHive addresses all three with hardware small enough to carry: it builds a
map as you walk, plots teammates on that map through obstructions, and lets you
issue commands by mouthing words silently.

Three subsystems, built to work together:

| Subsystem | Question it answers | Approach |
|---|---|---|
| **Mapping** | *Where am I?* | iPhone LiDAR → ARKit mesh → 2D overhead map on an embedded display |
| **Positioning** | *Where is everyone else?* | Dual UWB anchors ranging against teammate tags |
| **Silent speech** | *How do I talk without sound?* | Facial EMG → temporal CNN → word class, on-device |

---

## 1. Real-time mapping

An ARKit app on an iPhone 12 Pro drives the phone's LiDAR scanner to accumulate
a 3D mesh of the space as the operator walks. The mesh streams over a local
hotspot to a laptop, which slices a horizontal plane out of it at roughly waist
height — the cut that reads most like a floor plan — and renders that slice as a
2D overhead map. The map is then pushed to an ESP32 driving an LCD.

**Result:** live room mapping at **~12 fps** end to end, phone to embedded
display.

**Why a horizontal slice.** A raw LiDAR mesh is unreadable at a glance; it
contains the ceiling, furniture, and every partial surface the scanner caught at
an angle. Picking a single Z plane and projecting the intersections collapses it
into walls and doorways, which is the only part an operator actually needs to
navigate.

## 2. Teammate positioning

Two **NXP SR150** ultra-wideband transceivers act as anchors, reading UWB tags
carried by teammates. UWB was the right choice over BLE RSSI here: its
time-of-flight ranging gives decimeter-class distance rather than a signal
strength estimate that collapses in the presence of walls and bodies.

- **~240° of coverage** from the two-anchor geometry
- **Exponential moving average** smoothing on the range estimates, to suppress
  the jitter that multipath introduces indoors
- Each teammate renders as a red dot on the live map from subsystem 1

## 3. Silent-speech command recognition

The subsystem I built end to end — hardware, data collection, model, and
deployment target.

Three surface electrode channels on the face pick up the muscle activity of
*mouthing* a word with no voicing at all. A temporal convolutional network
classifies which word was mouthed, so the operator can issue commands in total
silence.

### Signal chain

16-bit ADC on an **NXP FRDM-MCXN947**, sampling three differential facial
channels (lips, cheekbone, masseter) at 200 Hz per channel, streamed over UART
to a host for collection and training. The trained INT8 model targets the
board's **eIQ Neutron NPU**.

### Measured results — locked test set, scored once

| Metric | Value |
|---|---|
| Chance (4 classes) | 25.0% |
| Classical baseline (RMS bins + RBF SVM) | 82.6% |
| **Per-window accuracy** | **90.8%** (1707 windows) |
| **Per-utterance accuracy** | **93.1%** (391 utterances) |
| **Streaming accuracy** | **88.6%** (219 words, 14.9 min silence) |
| False alarms while silent | 0.54 / minute |
| Model size | 22,744 bytes INT8 · 8,692 parameters |
| End-to-end latency | ~830 ms after speech onset |

Dataset: **11 continuous sessions, 81.9 minutes, 955 prompted utterances.**

Per-word streaming accuracy: `clear` 98.4%, `go` 89.7%, `help` 81.3%. The `help`
errors go almost entirely to `go` — a genuine articulatory collision, since both
involve lip movement, differing mainly in *when* the closure lands in the word.

### Three findings that mattered more than model architecture

| Change | Gain | Cost |
|---|---|---|
| 1.5 s window instead of 1.0 s | **+8.2 pts** per-window | None — offline re-cut |
| Per-channel gain augmentation | **+7.8 pts** per-window | None — training-time only |
| Envelope-gated streaming decision | **+38 pts** correct, **7× fewer** false alarms | One threshold |

None of the three required recording new data, and all three were only possible
because the recordings are **continuous streams** rather than pre-cut windows.
That protocol choice — recording the silence between words instead of only the
words — turned out to be the single highest-leverage decision in the project.

### Things that went wrong, and what they taught me

**Window accuracy does not predict live behaviour.** The model scored above 90%
on held-out windows while being nearly unusable in a live stream. Offline
evaluation only ever asks *which of four words is this?* — it never asks *is
this a word at all?*, which is the question that dominates real use, since the
operator is silent most of the time. Fixing this needed a separate
envelope-based gate deciding *when* to classify, not a better classifier.

**A 91% result that was a measurement artifact.** An early split put windows
from the same recording session on both sides of the train/test boundary.
Because consecutive windows overlap, the model was effectively scored on data it
had trained on. Splitting on session instead dropped the number substantially —
and made it real.

**An electrode can look alive and carry no signal.** One session recorded a
channel with a wandering, elevated baseline that passed every
is-this-electrode-connected check, but contained no prompt-locked activity at
all. The lesson: a movement check is not a signal check. The gate that replaced
it tests rest-versus-speak *contrast*, and the session was discarded.

**Why quantization was not optional.** INT8 costs ~1 point of accuracy against
float and takes the model to 22.7 kB — small enough to sit in flash alongside
the capture firmware. At 8,692 parameters the model runs on the Cortex-M33 in
single-digit milliseconds even without the NPU, which made NPU acceleration a
performance bonus rather than a dependency the demo could fail on.

---

## Stack

**Mapping** Swift, ARKit, iPhone 12 Pro LiDAR, ESP32, SPI LCD
**Positioning** NXP SR150 UWB, EMA filtering
**Silent speech** NXP FRDM-MCXN947 (Cortex-M33 + eIQ Neutron NPU), bare-metal C, 16-bit LPADC, TensorFlow/Keras, TensorFlow Lite Micro, INT8 post-training quantization, Python (NumPy, Matplotlib)
