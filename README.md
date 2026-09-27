# 7-inch Drone Project

GEPRC Mark4 7-inch build — Jetson Orin Nano 8GB, PX4 Kakute H7 V1.5. This repo holds
everything specific to *this* airframe (CAD, BOM, camera setup, `deploy/*.yaml`
device identity). Generic software lives in `haitomatic_stack`.

- [`BOM_and_ASSEMBLY.md`](BOM_and_ASSEMBLY.md) — bill of materials + build steps
- [`IMX219-160-IR-CUT_CAMERA_SETUP.md`](IMX219-160-IR-CUT_CAMERA_SETUP.md) — camera wiring/config
- [`deploy/`](deploy/) — `hw-config.yaml` + `sw-config.yaml`, deployed to `~/deployment/` on the Jetson

---

## Jetson Orin Nano/NX 40-pin header — pin allocation

The Kakute H7 FC connection already occupies **UART1** (pins 8/10) and uses **pin 6**
as its GND return (see `deploy/hw-config.yaml`: `flight_controller.uart:
/dev/ttyTHS1`). Any other peripheral wired directly to this header — e.g. a T3-S3
FDR over raw UART instead of USB — **must not** reuse pins 6, 8, or 10.

```
                 Jetson Orin Nano/NX Developer Kit -- J12/J14 40-pin header
                 ============================================================

                       +-----+-----+
          3.3V  1 --|(3V3)|(RED)|-- 2  5.0V
                       +-----+-----+
      I2C1_SDA  3 --|(   )|(   )|-- 4  5.0V
                       +-----+-----+
      I2C1_SCL  5 --|(   )|(## )|-- 6  GND         <- TAKEN (FC GND)
                       +-----+-----+
        GPIO09  7 --|(   )|(   )|-- 8  UART1_TXD   <- TAKEN (FC UART1 TX)
                       +-----+-----+
           GND  9 --|(## )|(   )|-- 10 UART1_RXD   <- TAKEN (FC UART1 RX)
                       +-----+-----+
    UART1_RTS* 11 --|(   )|(   )|-- 12 I2S0_SCLK
                       +-----+-----+
      SPI1_SCK 13 --|(   )|(## )|-- 14 GND
                       +-----+-----+
        GPIO12 15 --|(   )|(   )|-- 16 SPI1_CS1*
                       +-----+-----+
          3.3V 17 --|(3V3)|(   )|-- 18 SPI1_CS0*
                       +-----+-----+
     SPI0_MOSI 19 --|(   )|(   )|-- 20 GND
                       +-----+-----+
     SPI0_MISO 21 --|(   )|(## )|-- 22 SPI1_MISO
                       +-----+-----+
      SPI0_SCK 23 --|(   )|(   )|-- 24 SPI0_CS0*
                       +-----+-----+
           GND 25 --|(## )|(   )|-- 26 SPI0_CS1*
                       +-----+-----+
      I2C0_SDA 27 --|(   )|(   )|-- 28 I2C0_SCL
                       +-----+-----+
        GPIO01 29 --|(   )|(## )|-- 30 GND
                       +-----+-----+
        GPIO11 31 --|(   )|(   )|-- 32 GPIO07
                       +-----+-----+
        GPIO13 33 --|(   )|(## )|-- 34 GND
                       +-----+-----+
       I2S0_FS 35 --|(   )|(   )|-- 36 UART1_CTS*
                       +-----+-----+
     SPI1_MOSI 37 --|(   )|(   )|-- 38 I2S0_DIN
                       +-----+-----+
           GND 39 --|(## )|(   )|-- 40 I2S0_DOUT
                       +-----+-----+

   (3V3) = 3.3V rail   (RED) = 5.0V rail   (##) = GND   ( ) = signal / GPIO
```

### Pins reserved by the FC connection

| Pin | Signal | Used by |
|---|---|---|
| 6  | GND        | Kakute H7 FC — UART1 GND return |
| 8  | UART1_TXD  | Kakute H7 FC — `/dev/ttyTHS1` TX (uXRCE-DDS bridge, 921600 baud) |
| 10 | UART1_RXD  | Kakute H7 FC — `/dev/ttyTHS1` RX |

**Do not** wire a second peripheral (e.g. the T3-S3 FDR) to pins 6/8/10 — that UART is
already owned by the flight controller link. If the FDR needs a native header UART
(rather than USB, which is what `deploy/hw-config.yaml` currently specifies —
`fdr.port: /dev/ttyUSB0`), use **UART2** (`/dev/ttyTHS2`) or another free pin pair
instead of `/dev/ttyTHS1`.

---

## Known issue: Kakute H7 microSD card slot — not working

**Symptom:** PX4 boot log shows `INFO [init] formatting /dev/mmcsd0` immediately
followed by `ERROR [init] format failed`. `dataman` also fails to start (`Could not
open data manager file /fs/microsd/dataman`), and `health_and_arming_checks` reports
`Preflight Fail: Missing FMU SD Card`.

### What we've ruled out

Diagnosed live via NSH over MAVLink (`SERIAL_CONTROL`, same mechanism
`onboard-doctor`'s whitelisted commands use):

- `/dev/mmcsd0` **does** exist — the SPI-mode SD peripheral detects card presence at
  the hardware level. (Kakute H7 wires its SD card as SPI-mode, not native SDIO —
  `SPI::Bus::SPI1`, `SPIDEV_MMCSD(0)`, CS on `PortA/Pin4`, its own dedicated bus, not
  shared with the OSD (SPI2) or IMU (SPI4) — so this isn't SPI bus contention either.)
- `mkfatfs -F 32 /dev/mmcsd0` fails with a raw `I/O error` at the block-device level
  (not a filesystem-logic error) — same result with a **freshly known-good card**.
- **The card itself is confirmed healthy**: pulled it, ran a full non-destructive
  `badblocks -sv` scan on a desktop (26 min, all ~8M blocks) — `0 bad blocks found`.
  Write-protect was off. Reformatted clean (`mkfs.vfat -F 32`), verified with
  `fsck.vfat` (no errors). Put back in the FC — **identical `I/O error`**, ruling the
  card out conclusively.
- Not something introduced by any of our firmware work (Zenoh migration, module
  debloat) — this exact failure was already present before any of that, and nothing
  we changed touches SD/SPI1 config.

**Conclusion:** this points to a physical fault specific to *this* FC unit's SD slot
or its SPI1 wiring/connector (e.g. a cracked/cold solder joint, worn push-push
mechanism, bent CS pin) — not the card, not firmware/config. Needs physical inspection
(magnification on the connector solder joints) or swapping this exact card into a
different Kakute H7 unit to confirm conclusively. Not yet done.

### What this actually breaks (and what it doesn't)

Checked against PX4 source, not assumed:

| Affected | Not affected |
|---|---|
| **ULog flight log storage** — `logger` starts (`mode=all`) but has nowhere to write. No post-flight log retrieval possible in the current state. | **Arming** — `COM_ARM_SDCARD` defaults to `1` ("warning only"), doesn't block arming. |
| **`dataman`** fails to start — affects anything backed by it: complex uploaded polygon/plane geofences, waypoint mission storage, mission-resume-after-reboot state. None of these are used by this airframe (offboard+EV-only, no missions). | **The geofence we actually use** (`GF_MAX_HOR_DIST`/`GF_MAX_VER_DIST`, simple radius+altitude) — pure position math (`Geofence::isCloserThanMaxDistToHome`/`isBelowMaxAltitude`), no dataman dependency at all. |
| | **Flight control itself** — EKF2, attitude/position control, actuator output — zero SD dependency. |
| | **Parameter persistence** (`param save`) — this board migrated to **flash-based param storage** back in 2022 (see `boards/holybro/kakuteh7/init/rc.board_defaults`'s migration comment). Verified live: set a param, `param save`, full power-cycle reboot, value persisted with the `+` (saved) flag. Completely independent of the SD card. |

### Possible mitigation (not yet configured)

PX4's `logger` module already includes `log_writer_mavlink.cpp` — live ULog streaming
*over MAVLink* instead of to a file. Could let flight logs be captured on the
Jetson/ground side in real time without needing the SD slot fixed at all. Not
configured or tested yet.

### Bottom line

Not a blocker for flying — arming, control, geofence, and param persistence are all
unaffected. The real, standing loss is **no post-flight log retrieval**, which is
worth fixing properly (physical inspection of the SD connector) rather than living
with long-term.
