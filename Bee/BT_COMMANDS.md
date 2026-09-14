# 🐝 Bee Robot — Bluetooth Command Reference

Send any of these commands as plain text over Bluetooth (HC-05, 9600 baud).
Commands are **case-insensitive** (e.g. `forward`, `Forward`, `FORWARD` all work).

Every command runs to completion before the robot reads the next one — mid-routine
interruption isn't implemented yet (see the Roadmap in `README.md`).

---

## 🚶 Locomotion

| Command | Robot Response |
|---------|---------------|
| `forward` | Walk forward with arm counter-swing. |
| `backward` | Walk backward. |
| `left` | Pivot turn to the left. |
| `right` | Pivot turn to the right. |
| `circle` | Walk in a clockwise circle. |

---

## 💃 Dances

| Command | Robot Response |
|---------|---------------|
| `moonwalk` | Backward glide (lift → slide → drop technique). |
| `twist` | Hip swivel side-to-side on flat feet. |
| `jitter` | Fast, randomized full-body vibration. |
| `flap` | Feet flap like bird wings. |
| `skater` | Wide side-to-side speed-skater glide. |
| `swing` | Feet swing in and out while standing. |
| `sumostomp` | High leg lifts with heavy stomps. |
| `tiptoe` | Stands on inner edges of the feet. |
| `wobble` | Rapid side-to-side foot tilts. |
| `pigeon` | Knees rotate inward and outward. |
| `runningman` | Stationary running cycle. |
| `rock` | Full-body lean forward and backward. |
| `belly` | Opposite foot/leg wiggle. |
| `stanky` | One leg drags out to the side. |
| `charleston` | 1920s-style jazz swivel. |
| `booty` | Very fast leg-only vibration. |

---

## 🦾 Arm Gestures

| Command | Robot Response |
|---------|---------------|
| `wave` or `hello` | Right hand waves up and down. |
| `dab` | One arm across the chest, the other pointed up. |
| `zombie` | Both arms straight out in front. |
| `disco` | Alternating diagonal arm pumps. |
| `victory` | Alternating fist pumps in the air. |
| `swim` | Front-crawl swimming stroke. |
| `boxer` | Quick alternating punches. |
| `shrug` | "I don't know" shoulder shrug. |
| `think` | Hand to chin, "the thinker" pose. |
| `airplane` | Arms locked out wide while the body tilts side to side. |
| `handsup` | Both hands raised and held for ~1 second. |
| `salute` | Scrolls "ASSALAM WALAIKUM SIR" and performs a salute — used to greet teachers/guests during demos. |

---

## 😃 Emotions & LED Matrix Expressions

| Command | Robot Response |
|---------|---------------|
| `behappy` | Foot wiggle + "YES!" scrolls across the display. |
| `beconfused` | Feet tilt side to side. |
| `heartbeat` | Small heart → big heart, pulsing. |
| `wink` | Eyes normal → one eye closes. |
| `scanner` | Bright bar scans back and forth (Cylon/KITT style). |
| `matrixrain` | Matrix-style digital rain with falling columns. |
| `glitch` | Random static/glitch noise. |
| `spiral` | Spiral animation loads from outside to center. |
| `look` | Eyes look center → up → center → down. |
| `name` | Scrolls "MY NAME IS BEE". |
| `dissolve` | Pixels randomly turn off. Also runs automatically after every command completes. |

---

## 🎬 Full Routine

| Command | Robot Response |
|---------|---------------|
| `routine` | Full showcase sequence: "I AM BEE" → scrolling heart → "CITY COLLEGE" → "BEEEEEEEE...." + wink → heartbeat + skater glide → scanner + moonwalk → arms home + walk forward → look around → "BYE" → dissolve. |

---

## ℹ️ Utility & Control

| Command | Robot Response |
|---------|---------------|
| `home` | Reset to neutral standing position and clear the LED matrix. |
| `stop` | **Full shutdown.** Scrolls "Goodbye!!", dissolves the display, then detaches all 6 servos and powers down the LED matrix to save power. This is a sleep state, not just a pause — the robot needs a `start` command (or a physical reset) to move again. |
| `start` | Wakes the robot: re-attaches all servos, re-lights the LED matrix, plays a short intro animation. |

---

## 📏 Distance Readout

| Command | Robot Response |
|---------|---------------|
| `distance` | Reads the ultrasonic sensor and scrolls the measured distance (cm) across the LED matrix. |

There is currently no automatic/reactive distance behavior — the robot doesn't change what it's doing based on something approaching it. That kind of reactive behavior is a planned future feature, not a current one.

---

## 🔄 Quick Summary (All Confirmed Commands)

```
forward      backward     left          right        circle
moonwalk     twist        jitter        flap         skater
swing        sumostomp    tiptoe        wobble        pigeon
runningman   rock         belly         stanky        charleston
booty        wave         hello         dab           zombie
disco        victory      swim          boxer         shrug
think        airplane     handsup       salute        behappy
beconfused   heartbeat    wink          scanner       matrixrain
glitch       spiral       look          name          dissolve
routine      home         stop          start         distance
```

---

## 📝 Notes

- Commands run one at a time; a new command is only read once the current one finishes.
- `dissolve()` runs automatically after every processed command, in addition to being callable on its own.
- The robot is idle at boot until `startUp()`'s intro animation finishes and a command is sent.
