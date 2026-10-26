# Viktor Hexclaw :  6-DoF teleoperated robotic arm (glove-controlled)

This project is about the recreation of the **Hexclaw**, the robotic arm presentated by Viktor to Heinmerdinger in the 4th episode of the first season of Arcane. Here is a video focusing only on that passage : [the link](https://youtu.be/yBPGqcVmci8).

So the project is a low-cost 6-axis robotic arm built around Feetech STS3215 serial bus servos and controlled in real time by a wearable sensor glove. The arm is driven by an ESP32-S3; the glove runs on an ESP32-C3 (Seeed XIAO) and measures hand and arm orientation with IMUs and finger flexion with a flex sensor.

> [!NOTE]
> **Status: design phase. Nothing is built yet.**
> The mechanical concept, torque sizing, electronics and wiring are done on paper (see `docs/`). CAD modelling and the first hardware tests are next. I am updating this repository as the project moves forward.


## Specifications (target)

- 6 degrees of freedom plus a gripper, driven by 7 × Feetech STS3215 servos (12 V)
- Reach of about 53 cm (link lengths 12 / 16 / 15 / 10 cm between joints J2 to J5 and the gripper)
- Payload of 100 g at full extension (the gripper alone weighs about 100 g)
- Base rotation (J1) on a 120 mm lazy-Susan bearing
- Status light: NeoPixel Jewel (7 LEDs)
- Controller: ESP32-S3 DevKitC-1, with a Waveshare bus servo adapter

The dimensions come from scaling the arm to a human arm, not from a fixed design. My first scaled drawing gave a 157 cm arm, which was obviously too large. I then rescaled it using my own arm length (about 57–60 cm).

## Torque sizing

Assumptions: arm fully extended horizontally (worst case), payload 0.1 kg, gripper 0.1 kg, servo 0.05 kg each, links 0.3 kg in total (taken into account only for J1 inertia so far).

| Joint | Distance to the end (cm) | Static torque (kg·cm) |
|---|---|---|
| J5 | 10 | 2.0 |
| J4 | 25 | 6.25 |
| J3 | 41 | 12.3 |
| J2 | 53 | 18.55 |

**J1 (base rotation).** With a point-mass approximation, I = 0.65 kg × (0.35 m)² ≈ 0.08 kg·m². For a 90° move in 0.5 s with symmetric acceleration and deceleration, α = 4θ/t² ≈ 25 rad/s², so the required torque is about 2 N·m (about 20 kg·cm). The same move in 1 s needs about 5 kg·cm.

**Servo limits.** The STS3215 at 12 V is about 10 kg·cm nominal and about 30 kg·cm stall.

**Conclusion so far.** J4 and J5 are comfortable. J2 and J3 exceed the nominal torque even without link mass, and J2 would sit close to the stall torque once links are included.

## Electronics

**Arm**
- ESP32-S3 DevKitC-1 (5 V, about 200–300 mA)
- 7 × STS3215 (12 V, about 1 A rated, 2.7 A stall each)
- Waveshare bus servo adapter
- 12 V supply, two fuses, buck converter to 5 V
- NeoPixel Jewel with a 74AHCT125 level shifter, a 300–500 Ω series resistor on the data line, and a 100–1000 µF capacitor across the supply
- Switch, cables, screws

**Glove**
- Seeed XIAO ESP32-C3
- 2 × LSM6DSOX (6-axis IMU)
- Flex sensor with a 10–100 kΩ divider, and a potentiometer or rotary encoder [to be defined]
- LiPo 3.7 V, 110 mAh, switch and reset button

Wiring schematics are in `docs/` (scans of my hand-drawn drafts).