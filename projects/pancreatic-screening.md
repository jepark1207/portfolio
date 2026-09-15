# Preliminary Pancreatic Screening

**A cheap and efficient approach to employing convolutional neural networks on CT scans**
Aug 2023 – May 2024

[← Back to portfolio](../README.md)

---

## The problem

Cancer accounts for over **18 million new diagnoses** and nearly **10 million
deaths** each year. Pancreatic cancer is among the deadliest forms, and the
reason is specific: it is usually caught late. By the time symptoms bring a
patient in, treatment options are limited and the prognosis is poor.

Conventional radiological diagnosis also carries an inherent problem — it
depends on human interpretation, which introduces **subjectivity and variability
in accuracy** between readers. A screening tool that is consistent and cheap
enough to apply broadly attacks the problem at the point where it is actually
decided: detection timing.

## Approach

**Data.** A dataset of pancreatic CT scan images.

**Model.** A baseline CNN of **2 convolutional layers** followed by fully
connected layers for classification. From there I took a systematic, iterative
approach: add a layer, tune hyperparameters, measure, keep what helped. The
"preliminary" and "cheap" framing in the title is load-bearing — the goal was
explicitly a model that trains and runs without institutional compute, because a
screening tool nobody can afford to deploy does not screen anyone.

**Result: 96% classification accuracy on the test dataset.**

## Honest limitations

The value of the result depends on being clear about what it does *not* yet
establish. Four things warrant further investigation:

- **Dataset size** — accuracy on a limited dataset is not the same as accuracy in
  a clinic
- **Model interpretability** — a radiologist cannot act on a prediction with no
  stated basis, and a CNN offers none by default
- **Resource constraints** — the deliberate limit on compute also limits what
  architectures could be explored
- **Generalizability** — performance across scanners, protocols, and patient
  populations is untested

This is a **preliminary screening** result demonstrating feasibility, not a
diagnostic tool. Reducing reliance on subjective human reading could expedite
diagnosis and improve outcomes, but the gap between 96% on a test set and
clinical use is exactly the four items above.

## What I took from it

This was my first project where the model was the whole deliverable, and its
main lesson was methodological: **iterate systematically and measure every
change.** Adding layers one at a time and re-measuring — rather than building a
large model and tuning blindly — is the same discipline I applied later in
[ProsthetiSense](prosthetisense.md) and [ShadowHive](shadowhive.md), where
measuring each change independently was what separated real gains from noise.

It also taught me to be suspicious of a single accuracy number, which is why the
later projects report held-out and locked-set evaluation rather than one figure.

## Stack

TensorFlow/Keras · convolutional neural networks · Python (NumPy, Matplotlib) ·
medical imaging (CT) · hyperparameter optimization
