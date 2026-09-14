# Engineering Decisions — Bee Robot

A record of the key tradeoff decisions made during the build, including the problem, the options considered, what was chosen, why, the tradeoff accepted, and the result.

---

## 1. Compensating for a miscalibrated servo in software

**Problem:** The right leg servo didn't return to a true 0° position.

**Options:** Replace the servo, or compensate for its actual behavior in code.

**Chosen:** Compensate in code via the `RL_c` calibration constant.

**Why:** Avoided teardown time and cost close to a deadline, and the calibration-constant system already existed to support exactly this kind of adjustment.

**Tradeoff:** The robot's "true center" for that leg is a software correction rather than a physically matched value, so if that specific servo is ever swapped, the constant needs re-tuning.

**Result:** Stable, calibrated motion without hardware replacement.

---

## 2. Sourcing new Bluetooth modules instead of continuing to debug existing ones

**Problem:** The HC-05 modules on hand weren't holding a reliable connection, with the deadline approaching.

**Options:** Keep debugging the existing modules, or source and test new ones.

**Chosen:** Sourced and tested several additional HC-05 units through Kundu Electronics.

**Why:** Time pressure made a hardware swap more reliable than continuing to chase an intermittent fault with uncertain root cause.

**Tradeoff:** The original root cause of the faulty modules was never fully diagnosed.

**Result:** Stable Bluetooth communication, and the robot became properly interactive.

---

## 3. Teaching himself soldering rather than waiting on outside help

**Problem:** Planned help with soldering fell through.

**Options:** Find another person to help, or learn to solder and do it independently.

**Chosen:** Self-taught soldering.

**Why:** The project timeline didn't allow for an open-ended wait on outside help.

**Tradeoff:** Slower initial progress while learning the skill from scratch.

**Result:** Full electronics assembly completed independently, and a new hardware skill gained.

---

## 4. Deferring the ESP32 + Edge Impulse voice/AI experiment

**Problem:** Wanted voice/AI interaction via ESP32, Edge Impulse, and a microphone, in addition to full-body movement.

**Options:** Keep pushing to integrate voice/AI into a single ESP32-based build, or ship a feature-complete Nano-based robot now and revisit voice separately.

**Chosen:** Shipped the Nano-based robot without live voice, deferring voice to a planned DFPlayer Mini + speaker addition.

**Why:** Integrating voice/AI cleanly alongside all the movement and LED code wasn't realistic within the project's scope and timeline.

**Tradeoff:** The current robot "speaks" only through LED-matrix text, not audio.

**Result:** A complete, reliable robot delivered on time, with voice explicitly scoped as future work rather than a compromised, half-working feature crammed in.
