# ProsthetiSense

**Detecting finger movements for prosthetics using deep learning on non-invasive EMG signals**
Aug 2024 – May 2025 · **Team Lead**

[← Back to portfolio](../README.md)

---

## Recognition

- 🏆 **ISEF Finalist** — International Science and Engineering Fair
- 🏆 **Air Force Research Award**
- 🏆 **Regeneron Biomedical Engineering Award**
- Presented at **RRSEF**, **TXSEF**, and **ISEF**

## The problem

Limb loss affects over **50 million people** worldwide, and the annual number of
amputations continues to rise. The economics of the available options are stark:

| Option | Cost |
|---|---|
| Cosmetic, non-functional prosthetic | ~$5,000 |
| **Myoelectric (functional) prosthetic** | **$20,000 – $80,000** |
| An EMG sensor itself | $200 – $400 |

The sensing hardware is not what makes a myoelectric limb expensive — the
proprietary, experimental control systems built on top of it are. That gap is
the opening: if a deep learning model can be trained to recognize a small set of
broadly applicable gestures from cheap EMG hardware, the control system stops
being the cost driver, and functional prosthetics become far more accessible.

## Approach

**Data.** A dataset of wrist and hand movements recorded as electromyogram
signals across **8 separate channels** on the forearm. EMG measures the
electrical activity muscles generate when they contract, and it is *non-invasive*
— surface electrodes only, no implanted hardware and no surgery.

**Preprocessing.** Movements were categorized into a binary distinction —
**grabbing** versus **at rest**. Reducing the label space this way was
deliberate: a reliable two-state classifier that actually closes a hand is worth
more to a user than a ten-gesture model that misfires, and grab/release covers a
large share of real daily activity.

**Model.** A baseline neural network of **3 dense layers**, then systematic
hyperparameter tuning to find the configuration that extracted the most
structure from the 8-channel signal without overfitting.

**Hardware integration.** The model's output drove physical motors on a robotic
hand replicating human hand anatomy — the part that made this a control system
rather than a classification exercise. This required debugging across the
software/hardware boundary, where a model that looks correct offline still has
to produce motor commands at usable latency.

## Result

**94% test accuracy** on held-out data, driving a physical robotic hand.

Paired with simple motors, this demonstrates that a patient's own EMG data can
be calibrated to program responsive prosthetic movement at a fraction of current
cost — a genuinely accessible alternative control system.

## What this project led to

Leading ProsthetiSense is what made [ShadowHive's silent-speech
subsystem](shadowhive.md) possible a year later: the same class of problem
(surface EMG → neural network → real-time decision) but moved from the forearm
to the face, from a public dataset to hardware I built and data I collected
myself, and from offline evaluation to a quantized model deployed on a
microcontroller NPU. The failure modes I learned to look for here — electrode
placement sensitivity, and the gap between offline accuracy and live behaviour —
are the ones that dominated that project too.

## Stack

TensorFlow/Keras · Python (NumPy, Pandas, Matplotlib) · 8-channel surface EMG ·
robotic hand actuation · hardware/software integration

> *Accuracy note: my resume cites 95% for this project and the research abstract
> cites 94%. The 94% figure is from the final written abstract and is the one
> used here.*
