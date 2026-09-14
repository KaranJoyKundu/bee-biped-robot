<p align="center">
  <img src="media/images/photo1.jpg" width="48%">
  <img src="media/images/photo2.jpg" width="48%">
</p>



# 🐝 Bee Biped Robot

A 3D-printed, Bluetooth-controlled biped robot built to make microcontrollers fun to teach. Bee walks, dances, gestures with its arms, shows expressions on an 8x8 LED matrix face, and takes plain-text commands over Bluetooth.

**Author:** Karan Joy Kundu (ECE, Khulna University of Engineering and Technology)
**Built for:** Khulna City College, as a hands-on demonstration to make microcontroller concepts engaging for their students
**Status:** Completed working prototype, submitted 19 August 2026. Actively improved as time allows.
**Build time:** ~1.5 months, 50+ hardware/software iterations

---


## 🎥 Demo Video

*(Already posted to LinkedIn and Facebook — public link to be added here once available)*

---

## 📦 Components

| Component | Quantity | Purpose |
|-----------|----------|---------|
| Arduino Nano | 1 | Main controller |
| SG90 micro servo motor | 6 | Legs (2), feet (2), hands/arms (2) |
| MAX7219 8x8 LED matrix module | 1 | Facial expressions & text scrolling |
| HC-05 Bluetooth module | 1 | Wireless command input |
| HC-SR04 ultrasonic sensor | 1 | Distance measurement |
| Sunfort 1100 mAh battery | 2 (in parallel) | Power source |
| Power-bank boost module | 1 | Regulates battery output, provides Micro-USB charging |
| 3D-printed chassis (PLA/PETG) | 1 set | Body, customized from the Otto DIY base design |

**Mechanical base:** customized from [Otto DIY Humanoid Robot – The Fat Version](https://www.printables.com/model/225166-otto-diy-humanoid-robot-the-fat-version) on Printables. The final body, wiring, and firmware were fully assembled and customized by Karan; the Bluetooth, power, and switch layout followed the original design's arrangement as a starting point. Added on top of the base design: a hat (visual character + helps stabilize the robot) and a pair of shoes (visual character).

**Dimensions:** ~8 inches tall, ~9 inches wide with arms spread.

**Runtime:** roughly 6 hours per charge based on normal use (not lab-measured with a current meter).

### 🔮 Future Additions
- **DFPlayer Mini + 3W speaker** — voice output, to eventually replace/extend the LED-matrix-only "speech" (planned, not yet implemented)
- Revisit of an earlier **ESP32 + Edge Impulse + microphone** voice/AI experiment (see *Development Story* below) — worked in isolation but was shelved because integrating it alongside leg movement and the rest of the robot's code wasn't feasible in the project timeline

---

## 🔌 Pin Mapping

This reflects the actual wiring on the finished robot, confirmed against the sketch's own code (not the older planning comments that appeared at the top of some working drafts).

| Pin | Connected To | Notes |
|-----|-------------|-------|
| 0, 1 | Hardware Serial | USB debug / upload |
| 2 | — | Free / unused |
| 3 | rightLeg servo | Signal wire |
| 4 | HC-05 TX → Arduino RX | `SoftwareSerial` receive line |
| 5 | leftLeg servo | Signal wire |
| 6 | rightFoot servo | Signal wire |
| 7 | HC-SR04 TRIG | Ultrasonic trigger |
| 8 | MAX7219 CS | LED matrix chip select |
| 9 | leftFoot servo | Signal wire |
| 10 | leftHand servo | Signal wire |
| 11 | rightHand servo | Signal wire |
| 12 | MAX7219 DIN | LED matrix data in |
| 13 | MAX7219 CLK | LED matrix clock |
| A0 | HC-SR04 ECHO | Ultrasonic echo |
| A1 | — | Free / unused |
| A2 | HC-05 (Arduino TX line) | `SoftwareSerial` transmit line, physically wired |
| A3, A4, A5 | — | Free / unused |

> **Note on an earlier version of this table:** some working drafts of this documentation listed A1 as the Bluetooth TX pin and described A2 as a free/floating pin reserved for random-seed entropy. That was a planning note that was never carried into the actual build — the real, wired configuration uses A2 for Bluetooth TX, and A1 is the one that's free.

---

## 🧠 Architecture

Bee's motion system is **sequential and timing-based**: each gesture or dance is a dedicated function that writes servo positions in a fixed order, using `delay()` between steps to hold each pose. Commands arriving over Bluetooth are matched against a single, single-level `if / else if` ladder (30+ branches) using `String.equalsIgnoreCase()`, and the matched function then runs to completion before the loop reads the next command.

This is deliberately noted here rather than glossed over: a non-blocking, `millis()`-based motion scheduler (so a new Bluetooth command could interrupt a routine mid-playback) has been planned but is **not yet implemented** — see *Roadmap* below.

### Command Dispatch
`loop()` accumulates incoming Bluetooth bytes into a `String` buffer, trims it once a full command has arrived, and checks it case-insensitively against each known command in turn before resetting the buffer for the next command.

---

## 📡 Distance Sensing

The HC-SR04 ultrasonic sensor is wired and `getDistance()` is implemented. Its current use is an **on-demand readout**, triggered by the `distance` command: the sensor is read, the result is inverted with a `160 - distance` calculation, and the value is scrolled across the 8x8 LED matrix as text (e.g. `"XX CM"`).

There is currently **no autonomous, threshold-based reaction system** (e.g. the robot doesn't automatically change behavior as something approaches it) — that kind of distance-band reaction system has been discussed as a future direction but isn't built yet.

---

## ⚙️ Calibration

Six constants at the top of the sketch define each servo's neutral/center position:

```cpp
int RL_c = 113;   // Right Leg center
int LL_c = 135;   // Left Leg center
int RF_c = 120;   // Right Foot center
int LF_c = 100;   // Left Foot center
int RH_c = 120;   // Right Hand center
int LH_c = 120;   // Left Hand center
```

Nearly all motion functions compute their positions as offsets from these constants, which is what makes recalibrating for a different physical build straightforward.

---

## 🎮 Feature Roster & Bluetooth Commands

Send any of these as plain text over Bluetooth (HC-05, 9600 baud). Commands are **case-insensitive**.

### 🚶 Locomotion
| Command | Description |
|---------|-------------|
| `forward` | Walk forward, with arm counter-swing |
| `backward` | Walk backward |
| `left` | Pivot turn left |
| `right` | Pivot turn right |
| `circle` | Walk in a clockwise circle |

### 💃 Dances
| Command | Description |
|---------|-------------|
| `moonwalk` | Backward glide (lift-slide-drop technique) |
| `twist` | Hip swivel on flat feet |
| `jitter` | Fast randomized full-body vibration |
| `flap` | Feet flap like bird wings |
| `skater` | Wide side-to-side glide |
| `swing` | Feet swing in and out |
| `sumostomp` | High leg lifts with heavy stomps |
| `tiptoe` | Stand on inner foot edges |
| `wobble` | Rapid side-to-side foot tilts |
| `pigeon` | Knees rotate inward/outward |
| `runningman` | Stationary running cycle |
| `rock` | Full-body lean forward and back |
| `belly` | Opposite foot/leg wiggle |
| `stanky` | One leg drags out to the side |
| `charleston` | 1920s-style swivel |
| `booty` | Fast leg-only vibration |

### 🦾 Arm Gestures
| Command | Description |
|---------|-------------|
| `wave` / `hello` | Right hand waves up and down |
| `dab` | One arm across chest, one pointed up |
| `zombie` | Both arms straight out in front |
| `disco` | Alternating diagonal arm pumps |
| `victory` | Alternating fist pumps |
| `swim` | Front-crawl stroke motion |
| `boxer` | Quick alternating punches |
| `shrug` | "I don't know" shoulder shrug |
| `think` | Hand to chin, thinker pose |
| `airplane` | Arms locked wide, body tilts side to side |
| `handsup` | Both hands raised for ~1 second |
| `salute` | Scrolls a greeting ("ASSALAM WALAIKUM SIR") and salutes — used to greet teachers/guests during demos |

### 😃 Emotions & LED Expressions
| Command | Description |
|---------|-------------|
| `behappy` | Foot wiggle + "YES!" scroll |
| `beconfused` | Feet tilt side to side |
| `heartbeat` | Pulsing heart animation |
| `wink` | Eyes normal → wink |
| `scanner` | Cylon/KITT-style scanning bar |
| `matrixrain` | Falling-column digital rain effect |
| `glitch` | Random static/glitch noise |
| `spiral` | Spiral loading animation |
| `look` | Eyes look center → up → center → down |
| `name` | Scrolls "MY NAME IS BEE" |
| `dissolve` | Pixels randomly turn off (also runs automatically after every command) |

### 📏 Sensors
| Command | Description |
|---------|-------------|
| `distance` | Reads the ultrasonic sensor and scrolls the measured distance (cm) across the LED matrix |

### 🎬 Full Routine
| Command | Description |
|---------|-------------|
| `routine` | Full showcase: "I AM BEE" → scrolling heart → "CITY COLLEGE" → "BEEEEEEEE...." + wink → heartbeat + skater glide → scanner + moonwalk → arms home + walk forward → look around → "BYE" → dissolve |

### ℹ️ Utility & Control
| Command | Description |
|---------|-------------|
| `home` | Reset to neutral standing position, clear LED matrix |
| `stop` | **Full shutdown**: scrolls "Goodbye!!", dissolves the display, then detaches all 6 servos and powers down the LED matrix. This is a power-saving sleep state, not a simple pause. |
| `start` | Wakes the robot back up: re-attaches all servos, re-lights the LED matrix, plays a short intro |

---

## 🛠️ Known Engineering Notes & Cleanup Opportunities

Documented here for accuracy, not treated as urgent:

- Two near-identical wave functions (`wave()` and `Wave()`) exist from earlier iterations; only `Wave()` is currently wired to the `wave`/`hello` commands. Left as-is intentionally.
- `keepHandsDown()` writes fixed servo angles (`180`/`60`) rather than referencing the `RH_c`/`LH_c` calibration constants that the rest of the code uses. A natural future cleanup, not currently a priority.
- `randomSeed()` is not currently called in `setup()`, so the randomized animations (`jitter`, `matrixrain`, `glitch`) follow the same sequence on every power-up rather than varying.

---

## 🧗 Development Story

Bee went through more than 50 iterations over about a month and a half. A few of the harder problems along the way:

**Servo calibration:** the first leg servo didn't return to a true 0° position as expected. Rather than redesigning around it, the code was adjusted to compensate for that servo's actual behavior.

**Balance:** early walking attempts caused frequent falls. The 3D-printed PLA/PETG frame held up through repeated testing, and the walking gait was tuned through many rounds of trial and error until it became reliable.

**Development pause and return:** after the core walking motion was finally working, development paused for a while, the process had been physically and mentally demanding. Development resumed afterward with roughly 7 more days of focused work on the hand/arm gestures.

**Bluetooth reliability:** getting stable Bluetooth communication working was one of the biggest blockers, with the deadline approaching and the HC-05 modules on hand not cooperating. Several additional modules were sourced and tested through Kundu Electronics, Karan's own electronics business, before communication became reliable, a turning point that made the robot properly interactive.

**Soldering:** initial help with soldering fell through when a friend who'd agreed to assist wasn't able to follow through. Rather than stalling the project, soldering was learned from scratch and all wiring was completed independently.

**Finishing touches:** once the robot was functionally complete, a hat and a pair of shoes were added, partly for personality and partly because the hat helps stabilize the robot physically.

**Earlier direction not carried forward:** before settling on the current Nano-based build, an ESP32 + Edge Impulse + microphone voice/AI setup was tested and worked on its own, but integrating it alongside the leg movement and the rest of the robot's functionality wasn't practical within the project's scope and timeline. It's noted here as a real experiment, not as a current feature, and may be revisited later.

---

## 🔧 Setup & Upload

1. **Install dependencies** (Arduino IDE):
   - `Servo.h` — built-in
   - `LedControl.h` — install via Library Manager
   - `SoftwareSerial.h` — built-in

2. **Wire everything** according to the pin map above.

3. **Calibrate** the servo center values (`RL_c`, `LL_c`, etc.) so the robot stands straight.

4. **Upload** the sketch to an Arduino Nano.

5. **Connect** via Bluetooth (default baud: 9600) and send commands from any serial terminal app.

---

## 🗺️ Roadmap

- [x] Bluetooth command system with 30+ features
- [x] Walking, dances, arm gestures, LED facial expressions
- [x] On-demand ultrasonic distance readout
- [ ] Non-blocking, `millis()`-based motion scheduler
- [ ] Autonomous distance-band reaction system
- [ ] DFPlayer Mini + 3W speaker voice output
- [ ] Revisit ESP32 + Edge Impulse voice/AI experiment
- [ ] `randomSeed()` for non-repeating animations
- [ ] Unify duplicate wave functions and hand-position calibration references

---

## 📁 Project Structure

```
bee-robot/
├── README.md
├── BT_COMMANDS.md
├── LICENSE
├── src/
│   └── bee.ino
├── docs/
│   └── engineering-decisions.md
├── media/
│   ├── images/  
         ├── photo1.jpg
         └── photo2.jpg                  
│   └── demo/                      
└── 3d-parts/
    ├── body.stl
    ├── footl.stl
    ├── footr.stl
    ├── grip.stl
    ├── hat 1.0.STL
    ├── Hat 2.1 stl 2.STL
    ├── head.stl
    ├── leg shoe.SLDPRT
    ├── leg shoe.STL
    └── leg.stl
```

---

## 📜 License

MIT — feel free to modify, share, and build upon.



