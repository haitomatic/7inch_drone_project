# Bill of Materials & Assembly Guide

**Team EmberWatch** — Hardware Reference Document

---

## Bill of Materials (BOM)

| Item | Link |
|---|---|
| Radiomaster Boxer RC Transmitter | [radiomasterrc.com](https://radiomasterrc.com/products/boxer-radio-transparent-version) |
| 2x 1500mAh 22.2V 6S 100C LiPo (XT60) | [chinahobbyline.com](https://chinahobbyline.com/products/4-packs-cnhl-black-series-1500mah-22-2v-6s-100c-lipo-battery-with-xt60-plug) |
| 2x 1300mAh 22.2V 6S 130C LiPo (XT60) | [chinahobbyline.com](https://chinahobbyline.com/products/2-packs-cnhl-black-series-v2-0-1300mah-22-2v-6s-130c-lipo-battery-with-xt60-plug) |
| Main Camera — Waveshare IMX219-160 8MP IR-Cut | [arduinokauppa.fi](https://arduinokauppa.fi/produkt/waveshare-imx219-160-8mp-ir-cut-kamera-162-fov-ir-cut-infrapuna-sopii-jetson-nano-compute-module/) |
| Belly Gimbal Camera — Skydroid C10 Pro 1080p 3-axis stabilised | [balticdrones.eu](https://balticdrones.eu/products/fpv-hd-camera-skydroid-c10-pro-1080p-full-hd-3-axis-stabilisation-camera-gimbal) |
| RadioMaster RP3 ExpressLRS 2.4GHz Nano Receiver | [radiomasterrc.com](https://radiomasterrc.com/products/rp3-expresslrs-2-4ghz-nano-receiver) |
| TOF400C VL53L1X Time-of-Flight Rangefinder | [amazon.com](https://www.amazon.com/Teyleten-Robot-TOF400C-VL53L1X-Distance/dp/B0CLDKMGZR) |
| BEC (5V/12V) | [aliexpress.com](https://www.aliexpress.com/item/1005010721816413.html) |
| Holybro Kakute H7 v1.5 + Tekko32 F4 4-in-1 50A ESC | [holybro.com](https://holybro.com/products/kakute-h7-v1-stacks) |
| Xrotor 2807 1300KV Motors x4 | [hobbywinguav.com](https://hobbywinguav.com/product/2807/) |
| HQProp 7.5x3x3 Props (2CW + 2CCW) | [hqprop.com](https://www.hqprop.com/hqprop-75x3x3-light-grey-2cw2ccw-poly-carbonate-p0460.html) |
| Jetson Orin Nano 8GB Devkit | — |
| ToolkitRC M6D Charger | [oscarliang.com](https://oscarliang.com/toolkitrc-m6d-charger/) |
| GEPRC Mark4 7" V2 Frame | — |
| Tarot Battery Harness x3 | — |

---

## 3D Printed Parts

All parts printed before mechanical assembly. Start the print queue as early as possible — total print time ~9–10 hrs.

| Part | Qty | Material | Est. Print Time | Notes |
|---|---|---|---|---|
| **Tall landing gear legs** ✅ | 4 | PETG or ABS | ~~3 hr total~~ DONE | ~60–70 mm leg height — must clear belly gimbal + VL53L1X under frame |
| **Jetson Orin Nano top-plate mount** | 1 | PETG | ~2 hr | 100×79 mm footprint, M3 corner standoff holes, ventilation slots |
| **Belly gimbal mount bracket** | 1 | PLA | ~1 hr | Mounts Skydroid C10 Pro under bottom plate, centred between legs |
| **BEC tray / side bracket** | 1 | PLA | ~30 min | Secures BEC module to frame arm or spare plate area |
| **TPU anti-vibration FC spacers** | 4 | TPU 95A | ~1 hr | 3 mm thick, between ESC and FC in stack — optional but recommended |
| **VL53L1X rangefinder bracket** | 1 | PLA | ~30 min | Mounts TOF400C pointing straight down on frame underside |
| **Floor marker cones** | 4 | PLA (orange) | ~1 hr each / ~4 hr total | Flight zone markers for sports hall practice — standard traffic cone shape, ~150 mm tall. [Thingiverse example](https://www.thingiverse.com/search?q=traffic+cone) |

### Print priority order
1. ~~Landing gear legs~~ ✅ already printed
2. Jetson mount — needed before Step 7
3. Cones — print in parallel on a second printer or during free time; needed before first flight session
4. All others — needed before Step 6–8

### Material notes
- **PETG** — preferred for structural parts (legs, Jetson mount); better impact resistance than PLA, handles motor heat
- **ABS** — alternative to PETG for legs if you have an enclosure
- **PLA** — fine for camera brackets, BEC tray, rangefinder bracket, cones
- **TPU 95A** — mandatory for FC spacers; do not substitute PLA/PETG (defeats the purpose)

---

## Drone Assembly

### Hardware Feasibility Analysis

**Overall verdict: Flyable. Indoor localization via VIO (IMX219 + IMU) + VL53L1X rangefinder. No GPS needed.**

#### Component Inventory

| Component | Part | Status |
|---|---|---|
| Frame | GEPRC Mark4 7" V2 | ✅ |
| Flight Controller | Holybro Kakute H7 v1.5 | ✅ |
| ESC | Tekko32 F4 4-in-1 50A | ✅ |
| Motors | Xrotor 2807 1300KV x4 | ✅ |
| Props | HQProp 7.5x3x3 (2CW + 2CCW) | ✅ |
| Companion Computer | Jetson Orin Nano 8GB Devkit | ✅ |
| Main Camera | Waveshare IMX219-160 8MP | ✅ |
| Belly Gimbal Camera | Skydroid C10 Pro 1080p 3-axis gimbal | ✅ (non-critical — RTSP/WebRTC stream to GCS for operator SA) |
| RC Transmitter | Radiomaster Boxer | ✅ |
| RC Receiver | RadioMaster RP3 ExpressLRS 2.4GHz Nano | ✅ |
| Rangefinder (altitude) | TOF400C VL53L1X | ✅ |
| Batteries | 2x 1500mAh 6S + 2x 1300mAh 6S | ✅ |
| BEC | AliExpress 5V/12V BEC | ✅ |
| Battery Harness | Tarot x3 | ✅ |
| Charger | ToolkitRC M6D | ✅ |
| Wi-Fi | Jetson Orin Nano devkit onboard Wi-Fi | ✅ |
| VIO | IMX219 + Kakute H7 IMU → OpenVINS on Jetson | ✅ (software setup required) |
| Anti-vibration mount | — | ⚠️ Source or print |

---

#### ⚠️ Critical Issues (Must Address Before June 9)

**1. Jetson Orin Nano Devkit size and weight**
The devkit carrier board is 100×79 mm and ~260 g (module alone). Total drone AUW estimate:

| Part | Est. Weight |
|---|---|
| Frame (Mark4 7") | ~220 g |
| 4x Motors + ESC | ~255 g |
| FC | ~20 g |
| Jetson Orin Nano devkit | ~280 g |
| BEC + wiring | ~40 g |
| Camera(s) | ~30 g |
| 3D printed parts | ~60 g |
| 1500mAh 6S battery | ~215 g |
| **Total estimate** | **~1120 g** |

2807 1300KV on 6S with 7.5" props can push ~1.2–1.4 kg thrust per motor (4.8–5.6 kg total). A ~1.1 kg drone has a reasonable ~4:1 thrust-to-weight ratio. **Flyable, but not efficient.** Use the lighter 1500 mAh batteries for the contest flight and keep maneuvers gentle.

**2. IMX219 CSI cable compatibility with Orin Nano Devkit**
The standard Waveshare IMX219-160 uses a **15-pin FFC** cable (Jetson Nano pinout). The Jetson Orin Nano devkit has **22-pin MIPI CSI** connectors. You need either:
- Waveshare's adapter board for Orin (they make one), or
- A 15-to-22-pin CSI adapter ribbon cable.
**Verify this before trusting the camera works on Orin.** Order the adapter now if you don't have it.

**3. Control link cut mechanism**
The rules require physically cutting the control link after launch. With the Radiomaster Boxer + ExpressLRS, the practical approach is to simply **power off the transmitter** at the predefined distance. Confirm with organizers this satisfies the rule (it is the most common interpretation).

---

### Assembly Quick Reference

```
STEP 1 — PRINT PARTS
┌─────────────────────────────────────────────────────┐
│  [Landing Legs x4]  [Jetson Mount]  [Belly Cam Brkt] │
│  [BEC Tray]         [TPU FC Spacers - optional]      │
└─────────────────────────────────────────────────────┘
                         │
                         ▼
STEP 2 — FRAME
┌──────────────────────────────────┐
│   TOP PLATE                      │
│   ┌──┐  ARM◄──┐  ┌──►ARM  ┌──┐  │
│   │  │        │  │        │  │  │
│   └──┘  ARM◄──┘  └──►ARM  └──┘  │
│   BOTTOM PLATE                   │
│   └── attach 3D printed legs ────┘
└──────────────────────────────────┘
                         │
                         ▼
STEP 3 — MOTORS + ESC WIRING
┌─────────────────────────────────────┐
│  Motor wires routed through arms    │
│  Pre-tin ESC pads (don't solder yet)│
│  Solder XT60 battery lead to ESC   │
└─────────────────────────────────────┘
                         │
                         ▼
STEP 4 — FC STACK (bottom → top)
┌──────────────────────────────┐
│  BOTTOM PLATE                │
│  ├── ESC (Tekko32 F4 4in1)   │
│  ├── [TPU spacers optional]  │
│  └── FC (Kakute H7)          │
│       └── connect ESC signal │
└──────────────────────────────┘
                         │
                         ▼
STEP 5 — POWER / BEC
┌──────────────────────────────────────────┐
│  BATTERY XT60 male                       │
│  └── XT60 female                         │
│       ├── 12 AWG → Tekko32 ESC           │
│       └── 16 AWG → UBEC Duo             │
│                     ├── 12V → Jetson    │
│                     └── 5V  → spare     │
│  VBAT wire → Kakute H7 (voltage mon.)   │
└──────────────────────────────────────────┘
                         │
                         ▼
STEP 6 — VL53L1X RANGEFINDER
┌─────────────────────────────────────────┐
│  TOF400C board → bottom plate (down)    │
│  I2C cable → Kakute H7 I2C pads        │
│  ArduPilot: RNGFND1_TYPE=16            │
└─────────────────────────────────────────┘
                         │
                         ▼
STEP 7 — JETSON ORIN NANO
┌─────────────────────────────────────────────┐
│  3D printed mount → M3 standoffs on FC     │
│  Jetson devkit → foam tape → printed mount  │
│  BEC 5V → Jetson barrel/GPIO               │
│  Jetson UART TX/RX ↔ Kakute H7 UART (MAVLink)│
└─────────────────────────────────────────────┘
                         │
                         ▼
STEP 8 — CAMERAS
┌──────────────────────────────────────────────────┐
│  TOP (forward)                                   │
│  IMX219-160 → CSI adapter → Jetson CAM0         │
│                                                  │
│  BELLY (gimbal)                                  │
│  Skydroid C10 Pro → 3D mount → bottom plate     │
│  Power: BEC 12V rail                            │
│  Video: RTSP over Wi-Fi → GCS (SA only)        │
└──────────────────────────────────────────────────┘
                         │
                         ▼
STEP 9 — MOTORS + PROPS
┌────────────────────────────────────────────┐
│  Power up (no props) → Mission Planner     │
│  Motor test → verify spin direction        │
│  Swap 2 phase wires if wrong direction     │
│                                            │
│  X-frame spin map:                         │
│   CCW(FL) ──┐  ┌── CW(FR)                │
│              ╳                             │
│    CW(RL) ──┘  └── CCW(RR)               │
│                                            │
│  Install props: CW→CW, CCW→CCW            │
└────────────────────────────────────────────┘
                         │
                         ▼
STEP 10 — FINAL CHECKS
┌──────────────────────────────────────────────┐
│  ✓ All bolts torqued                         │
│  ✓ BEC voltage = 5.0–5.2V at Jetson         │
│  ✓ CSI cameras detected on Jetson            │
│  ✓ MAVLink link confirmed (MAVROS)           │
│  ✓ VL53L1X reading in Mission Planner        │
│  ✓ EKF3 params set for VIO + rangefinder     │
│  ✓ OpenVINS vision_pose topic publishing     │
│  ✓ Indoor altitude hold tested               │
└──────────────────────────────────────────────┘
```

---

### Step-by-Step Assembly Guide

> **Tools needed:** Phillips and hex (M3) screwdrivers, soldering iron + solder, heat shrink, double-sided tape or non-permanent threadlocker (Loctite 222), multimeter, zip ties, XT60 connectors.

---

#### Step 1 — Print Required Parts

Print all parts before any mechanical assembly so they are ready when needed.

| Part | Notes |
|---|---|
| **Tall landing gear legs (x4)** | Must clear belly camera + gimbal mount. Recommend ~60–70 mm leg height. Print in PETG or ABS for impact resistance. |
| **Jetson Orin Nano top plate mount** | Sized to the 100×79 mm devkit board. Include M3 standoff holes at corners and ventilation slots. Print in PETG. |
| **Belly camera mount / fixed tilt bracket** | For IMX219 under the belly, angled ~30° forward-down. Use M2 screws. Print in PLA is fine. |
| **BEC tray or side bracket** | Small tray to secure the BEC module on a frame arm or stack. |
| **Anti-vibration FC spacers** | Optional: soft TPU spacers between FC stack and frame, 3 mm thick. Print in TPU 95A. |

---

#### Step 2 — Frame Assembly

1. Lay out all Mark4 7" V2 frame parts. Identify top plate, bottom plate, 4 arms, and motor standoffs.
2. Slide the 4 arms into the bottom plate slots. Align CW/CCW arm markings with your motor layout (front-left CW, front-right CCW, rear-left CCW, rear-right CW for standard X configuration).
3. Place the top plate over the arms. Insert and snug (do not fully torque) the main frame M3 bolts — you will need access to the stack later.
4. Attach the 3D printed **tall landing gear legs** to the bottom plate corners using M3 screws. Confirm the legs are long enough that the belly camera clears the ground with ~10–15 mm margin.

---

#### Step 3 — Motor and ESC Wiring

1. Thread each motor's 3 phase wires up through the arm channel toward the centre stack.
2. Do **not** solder the motor leads to the ESC yet — spin direction is confirmed after first power-up.
3. Pre-tin the ESC motor pads and the motor wire tips.
4. Temporarily twist and hold motor leads to ESC pads in default order (A→A, B→B, C→C). You will swap two wires on any motor that spins the wrong direction in Step 9.
5. Solder the **battery lead XT60** to the ESC power input pads. Observe polarity (red = positive). Apply heat shrink.

---

#### Step 4 — ESC and Flight Controller Stack

1. Stack order (bottom to top): **Bottom plate → ESC → FC spacers → Kakute H7**.
2. Use the M3 nylon standoffs included with the Kakute kit. If using printed TPU anti-vibration spacers, place them between ESC and FC.
3. Route the ESC-to-FC signal harness (typically 8-pin or individual dupont) to the FC motor signal pads (M1–M4) and GND.
4. Connect the ESC telemetry wire to FC UART RX if you want RPM telemetry (optional but recommended for ArduPilot).
5. Tighten all stack bolts to finger-tight + quarter turn. Do not overtorque — the FC PCB corners can crack.

---

#### Step 5 — Power Distribution and BEC

**No PM06 / PDB needed** — the Tekko32 4-in-1 ESC has a single XT60 input and handles its own distribution. Power is split right after the battery connector:

```
Battery XT60 male
        │
  XT60 female (on drone harness)
        │
    solder joint / wire split
   ┌────┴────────┐
Tekko32 ESC    UBEC Duo input
(12 AWG)       (16 AWG)
```

1. Solder the XT60 female to a short section of 12 AWG wire (10–15 cm), then split into two branches at a tinned solder joint.
2. Branch 1 (12 AWG): to Tekko32 ESC XT60 input pads.
3. Branch 2 (16 AWG): to UBEC Duo input pads.
4. Add a **1000–2200 µF 35V electrolytic capacitor** across the ESC power pads, as close to the Tekko32 as possible — reduces voltage spikes from motor switching.
5. Configure UBEC Duo outputs: **12V** for Jetson Orin Nano barrel jack, **5V** spare rail.
6. Solder a thin wire from the battery + and − to the Kakute H7 `VBAT` and `GND` pads for voltage monitoring. Set `BATT_MONITOR=3` in ArduPilot (voltage only — no current sensor).
7. Route UBEC output wires toward the top of the stack but do not connect to Jetson yet.
8. Attach the UBEC Duo to the frame using the 3D printed BEC tray or double-sided tape on a spare frame plate area.

---

#### Step 6 — VL53L1X Rangefinder Mounting

1. Mount the TOF400C board on the underside of the bottom plate or on a small printed bracket, pointing straight down. Centre it between the landing legs.
2. Route the I2C cable (SDA, SCL, VCC 3.3V, GND) up through the stack to the Kakute H7 I2C pads.
3. In ArduPilot set `RNGFND1_TYPE=16` (VL53L1X), `RNGFND1_ADDR=0x29`, `RNGFND1_MAX_CM=400`, `RNGFND1_MIN_CM=5`, `RNGFND1_ORIENT=25` (down).
4. Confirm readings in Mission Planner under Status → sonarrange before first flight.

---

#### Step 7 — Jetson Orin Nano Mounting

1. Place the 3D printed Jetson mount plate on top of the FC stack, secured via M3 standoffs (~25 mm height to give stack clearance).
2. Use M3 screws through the printed mount into the standoffs. Do **not** bolt the Jetson devkit directly to vibrating carbon fibre — use the printed mount as a vibration-damping interface (add a thin layer of foam tape between Jetson board and printed mount).
3. Place the Jetson Orin Nano devkit onto the printed mount. Secure with M3 bolts through the devkit board mounting holes at the four corners.
4. Connect the BEC 5V output to the Jetson devkit 5V barrel jack or 5V GPIO pin (pin 2 or 4 on the 40-pin header + GND on pin 6).
5. Connect Jetson UART to Kakute H7 UART1 (uXRCE-DDS / MAVLink bridge):

   **Jetson Orin Nano 40-pin GPIO header:**
   ```
   Pin 1  = 3.3V          Pin 2  = 5V
   Pin 3  = I2C SDA       Pin 4  = 5V
   Pin 5  = I2C SCL       Pin 6  = GND  ← GND wire here
   Pin 7  = GPIO          Pin 8  = UART TX (TXD) ← Jetson TX
   Pin 9  = GND           Pin 10 = UART RX (RXD) ← Jetson RX
   ```

   **Kakute H7 UART1 pads** (silkscreen: `TX1` / `RX1`, near USB-C):
   ```
   TX1 pad  → Jetson pin 10 (RX)
   RX1 pad  → Jetson pin 8  (TX)
   GND pad  → Jetson pin 6  (GND)
   ```

   > Cross-connect: Jetson **TX** → Kakute **RX**, Jetson **RX** → Kakute **TX**. Never TX→TX.

   **Wire spec:** 26–28 AWG stranded, ~20cm, no need for shielding at this baud rate.

   **Baud rate:** 921600 (set on both sides — PX4 `SER_TEL1_BAUD=921600`, Jetson MicroXRCE agent `-b 921600`).

   **Voltage:** Kakute H7 UART pins are **3.3V logic**. Jetson GPIO is also 3.3V — direct connection, no level shifter needed.

---

#### Step 8 — Camera Installation

**Main forward camera (top of frame):**
1. Mount the IMX219-160 facing forward on the front of the top plate using the printed bracket or with double-sided foam tape.
2. Route the CSI ribbon cable to the Jetson Orin Nano CSI port (CAM0).
3. If your IMX219 has a 15-pin cable and the Orin Nano devkit has 22-pin connectors, attach the Waveshare 15→22 pin CSI adapter first, then plug in.

**Belly/gimbal camera (under frame — Skydroid C10 Pro):**

> Not mission-critical for autonomy. Primary role is RTSP/WebRTC live stream to the GCS for operator situational awareness during the autonomous phase (monitoring only — no control input). Fire/people detection runs on the main IMX219 forward camera.

1. Attach the 3D printed belly gimbal mount to the underside of the bottom plate, centred between the landing legs.
2. Mount the Skydroid C10 Pro gimbal into the bracket using its own mounting plate. Secure with M3 screws.
3. Power the C10 Pro from the BEC 12V rail (check C10 Pro input voltage spec — typically 7–26V, so 6S direct tap is also viable via a voltage-appropriate power lead).
4. Connect the C10 Pro to the Jetson via its video output. The C10 Pro supports RTSP — expose this stream over Wi-Fi or a tethered link to the GCS for monitoring.
5. Confirm gimbal stabilisation is active (3-axis self-levels on power-up). No further software integration needed for the autonomous mission itself.

---

#### Step 9 — Props, Motor Direction, and First Power-Up

1. Without props installed, power up the drone via battery (do not arm).
2. Connect ArduPilot Mission Planner via USB to the Kakute H7.
3. Run **motor test** (in Mission Planner: Optional Hardware → Motor Test) — spin each motor individually at 10% throttle.
4. Verify each motor spins in the correct direction. For X-frame: M1 (front-right) CCW, M2 (rear-left) CCW, M3 (front-left) CW, M4 (rear-right) CW.
5. For any motor spinning the wrong way: power off, swap **any two of the three phase wires** on that motor at the ESC pad, re-solder.
6. Install props: **CW props on CW motors, CCW props on CCW motors.** Press firmly until the prop nut seats and tighten with the prop tool.

---

#### Step 10 — Final Checks Before Indoor Test

- [ ] All bolts torqued (M3 frame bolts, motor bolts, stack bolts, prop nuts)
- [ ] All solder joints inspected — no bridges, cold joints, or bare wires touching carbon
- [ ] BEC output voltage confirmed at Jetson power input with multimeter (should be 5.0–5.2V)
- [ ] CSI cameras detected by Jetson (`nvgstcapture` or `v4l2-ctl --list-devices`)
- [ ] ArduPilot FC-to-Jetson MAVLink link confirmed (check MAVROS `ros2 topic echo /mavros/imu/data`)
- [ ] VL53L1X rangefinder reading confirmed in Mission Planner (`sonarrange` value changes with hand under sensor)
- [ ] ArduPilot EKF3 configured for VIO: `EK3_SRC1_POSXY=6`, `EK3_SRC1_VELXY=6`, `EK3_SRC1_POSZ=2` (rangefinder)
- [ ] OpenVINS (or VINS-Mono) `vision_pose` topic verified publishing on Jetson before arming
- [ ] Indoor altitude hold tested (rangefinder Z + barometer) in a safe space before contest day

---

## Battery Charging Guide

### Charger: ToolkitRC M6D
The M6D has **two independent charge channels**. Always charge both packs of a pair simultaneously — one pack per channel.

---

### Charge settings per pack

| Pack | Cell count | Full voltage | Charge rate (1C) | Charge rate (2C) | Time at 2C |
|---|---|---|---|---|---|
| 1x 1500mAh 6S | 6S | 25.2V (4.20V/cell) | 1.5A | 3.0A | ~35 min |
| 1x 1300mAh 6S | 6S | 25.2V (4.20V/cell) | 1.3A | 2.6A | ~35 min |

> CNHL Black Series are rated 100C discharge — 2C charge is well within spec and safe.

**M6D settings (per channel):**
- Mode: `BALANCE CHARGE`
- Chemistry: `LiPo`
- Cell count: `6S`
- Current: `3.0A` for 1500mAh / `2.6A` for 1300mAh
- Cutoff voltage: `4.20V/cell` (default)

---

### Charging pairs for test sessions

Both packs of a pair charge simultaneously on separate channels:

```
M6D Channel 1 ──► Pack A (e.g. 1500mAh #1)  ┐
M6D Channel 2 ──► Pack B (e.g. 1500mAh #2)  ┘  both done ~35 min
```

After charging, both packs will be at the same voltage — safe to connect in parallel on the drone immediately.

---

### Session battery plan

| Session | Pair | Combined capacity | Est. usable flight time |
|---|---|---|---|
| Test run 1 | 2x 1500mAh | 3000mAh | ~11–13 min |
| Test run 2 | 2x 1300mAh | 2600mAh | ~9–11 min |
| **Contest run** | **2x 1500mAh** | **3000mAh** | **~11–13 min** |

Always use the **1500mAh pair for the contest run**.

---

### Day-before contest procedure

1. Charge all 4 packs to full (4.20V/cell) the evening before.
2. Store overnight. Check voltage morning of contest — packs should still read ≥ 4.18V/cell.
3. If any pack dropped below 4.15V/cell overnight, top-charge again before leaving.
4. At the venue, do not leave packs at full charge sitting idle for more than 2–3 hours — they will begin self-discharging and the cells can become slightly unbalanced.

---

### Storage charging (after contest / end of day)

If packs are not being used within 24 hours, discharge to **storage voltage: 3.85V/cell (23.1V for 6S)** using the M6D `STORAGE` mode. Leaving LiPo packs at full charge degrades cell chemistry over days/weeks.

---

### Safety rules

- **Never charge unattended** — always have someone present or use a LiPo-safe bag
- **Never charge a puffed or damaged pack** — discard it safely
- **Never parallel-charge mismatched voltage packs** — balance first or charge separately
- **Always check polarity** before connecting XT60 — the M6D will error on reverse polarity but connectors can be forced