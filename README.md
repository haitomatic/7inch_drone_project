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
