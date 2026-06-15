---
title: "Why the LEDs Would Not Blink When Connected in Parallel to a 556 Circuit"
date: 2026-06-08
draft: false
---

## Context
Traffic light subsystem for ENG1013. A 556 dual timer generates a flashing signal for warning lights (WL2). The output (pin 5 or 9) was supposed to drive two red LEDs that flash alternately.

## Symptom
When I connected the two LEDs in parallel directly to the output pin, neither blinked. The 556's output measured a flat ~0.6 V. Removing one LED made the other blink faintly. Something was dragging the output low.

## Investigation
- Checked the 556 timing: RC values were correct, astable mode confirmed.
- Scoped the output pin without load: clean square wave, 2.5 Hz.
- Suspected the output couldn't supply enough current for two LEDs in parallel.

Measured each LED's forward voltage: one was 1.82 V, the other 1.85 V. Connected them in parallel with a single 220 Ω resistor to Vcc (5 V). The LED with the lower Vf hogged almost all the current – the other stayed dark.

## Root Cause
**Parallel LEDs with a shared current-limiting resistor are unstable.** The 556's output stage (a bipolar totem pole) has limited source/sink capability (~100–200 mA). When forced into a current-hogging situation, the output voltage collapses as the transistor saturates and cannot drive the non-matched LED.

Additionally, the 556's output pin sources current when high, but I had wired the LEDs as active-low (cathode to output, anode to Vcc through resistor). This confused the expected logic – the output sinking current when low should turn LEDs on, but the shared resistor still caused imbalance.

## Fix
1. **Independent resistors:** Each LED got its own 220 Ω resistor, either from Vcc to anode (active-low) or from cathode to GND (active-high). Both worked.
2. **Confirmed wiring:** In active-low configuration, when the output went low, both LEDs turned on equally. Blinking returned immediately.

## Lesson
- Never parallel LEDs without individual current-limiting resistors. The one with slightly lower Vf steals current, leading to one bright LED and one off, or total output collapse if the driver can't maintain voltage.
- Always check the datasheet for output drive capability: the 556 can sink/source decent current, but a short or near-short (unequal parallel LEDs) can overload it.
- A simple scope check of the output pin under load pinpointed the collapse.

## Schematic Snippet
(A hand-drawn sketch of the correct wiring with separate resistors will be scanned and added later.)