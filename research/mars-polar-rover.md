# Mars Polar Rover — Power Team

**Stanford Space Initiative** · 2026

[← Back to portfolio](../README.md)

---

## The project

Design and test a rover capable of traversing Antarctica. The polar environment
is the entire design driver: extreme cold degrades battery capacity, the sun sits
low and moves continuously so solar harvest is never at a fixed operating point,
and there is no one nearby to recover a rover that browns out.

The long-term vision is to deploy in **Alaska and Antarctica** and collect data
for Stanford University research.

I'm on the **Power Team**, where I designed a power management unit PCB.

## Methods

### Power management unit PCB

Designed the PMU board that takes solar input, manages the battery, and supplies
the rover's loads. On a vehicle that cannot be recovered if it fails, the power
system is the subsystem where a mistake is permanent.

### MPPT solar tracking

Implemented a **maximum power point tracking** system to optimize power
extracted from the panels in extreme conditions.

MPPT matters specifically because of where this rover operates. A solar panel's
current-voltage curve has one point of maximum power, and that point moves with
irradiance and temperature — both of which shift constantly in polar conditions,
where the sun is low and the panel is cold. Connecting the battery directly to
the panel clamps the operating point to the battery's voltage, which is almost
never the maximum power point. MPPT continuously searches for that point
instead, and the margin it recovers is the difference between a rover that
finishes its traverse and one that doesn't.

### Thermal sensing

Configured the thermal sensing unit and programmed the MCU to measure internal
and external temperature using **thermistors**.

Two measurements, two purposes: external temperature characterizes the
environment, while internal temperature guards the battery — lithium chemistry
must not be charged below freezing without damage, so the charge controller has
to know the pack's actual temperature, not the air's. Thermistors are
nonlinear, so the firmware converts resistance to temperature rather than
reading a voltage proportional to it.

### Autonomy prototyping

Prototyped a scaled rover to test **LiDAR**, and programmed a **Jetson Nano** to
test the global path planning system — validating the navigation stack on a
small platform before committing it to the full vehicle.

## Stack

KiCad · MPPT power electronics · thermistor thermal sensing · embedded C ·
LiDAR · Jetson Nano · global path planning · battery management
