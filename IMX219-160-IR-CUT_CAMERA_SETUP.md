# Waveshare IMX219-160 IR-CUT Camera — Setup on Jetson Orin Nano

Source: https://www.waveshare.com/wiki/IMX219-160_IR-CUT_Camera (archived)
Board: NVIDIA Jetson Orin Nano Developer Kit (tegra234-p3768-0000 + p3767-0005)
JetPack: 6.2 (L4T R36.4.7)
Camera connected to: **CAM1 (J6) = CSI port C = serial_c**

---

## 1. Hardware Connection

- Waveshare IMX219-160 uses a **15-pin FFC** connector (RPi-style).
- Jetson Orin Nano devkit has **22-pin** CSI connectors.
- **Requires a 15-to-22 pin CSI FPC adapter** — metal contacts facing heatsink when inserting.
- Camera is plugged into **CAM1 (J6)** on the devkit.

### IR-CUT GPIO control
- High level (3.3V) → IR filter OFF → **night vision mode**
- Low level (GND)  → IR filter ON  → **daytime mode**
(Reddish images during day = IR filter is off — normal in night mode)

---

## 2. Device Tree Overlay

The correct overlay for CAM1 (port C) is already pre-installed in JetPack 6:

```
/boot/tegra234-p3767-camera-p3768-imx219-C.dtbo
```

Applied via NVIDIA's `config-by-hardware.py`:

```bash
sudo python3 /opt/nvidia/jetson-io/config-by-hardware.py -n 2='Camera IMX219-C'
sudo reboot
```

This creates a `JetsonIO` label in `/boot/extlinux/extlinux.conf` with:
```
FDT /boot/dtb/kernel_tegra234-p3768-0000+p3767-0005-nv.dtb
OVERLAYS /boot/tegra234-p3767-camera-p3768-imx219-C.dtbo
```

---

## 3. Verify Camera After Reboot

```bash
# Check kernel detected the sensor
sudo dmesg | grep -i 'imx219\|nvcsi\|tegra-vi'
# Expected: imx219 detected at address 0x10

# Check video device appeared
ls /dev/video*        # should show /dev/video0

# I2C scan — IMX219 sits at address 0x10
i2cdetect -l          # find camera I2C bus
sudo i2cdetect -y -r 9    # adjust bus number as needed

# V4L2 device info
v4l2-ctl --list-devices
v4l2-ctl -d /dev/video0 --list-formats-ext
# Expected formats: SRGGB10 (RG10) at 3264x2464, 1920x1080, 1280x720 etc.
```

---

## 4. Test Camera Capture

### Quick test (Jetson-native, requires display)
```bash
DISPLAY=:0.0 nvgstcapture-1.0
# or specify sensor:
DISPLAY=:0.0 nvgstcapture-1.0 --sensor-id=0
```

### GStreamer — headless JPEG capture (no display needed)
```bash
gst-launch-1.0 -e \
  nvarguscamerasrc sensor-id=0 num-buffers=1 ! \
  'video/x-raw(memory:NVMM),width=1920,height=1080,framerate=30/1' ! \
  nvvidconv ! nvjpegenc quality=90 ! \
  filesink location=/tmp/imx219_test.jpg
```

### GStreamer — live preview (X11 forwarding)
```bash
gst-launch-1.0 \
  nvarguscamerasrc sensor-id=0 ! \
  'video/x-raw(memory:NVMM),width=1920,height=1080,framerate=30/1' ! \
  nvvidconv ! nvegltransform ! nveglglessink -e
```

> **Note:** Orin Nano has **NO NVENC hardware encoder**. Use `x264enc` (software) instead of `nvv4l2h264enc` for video recording.

### GStreamer — record video (software encode)
```bash
gst-launch-1.0 -e \
  nvarguscamerasrc sensor-id=0 ! \
  'video/x-raw(memory:NVMM),width=1920,height=1080,framerate=30/1' ! \
  nvvidconv ! 'video/x-raw,format=I420' ! \
  x264enc tune=zerolatency bitrate=4000 speed-preset=ultrafast ! \
  h264parse ! mp4mux ! filesink location=/tmp/imx219_clip.mp4
```

---

## 5. ISP Color Fix (if image is reddish)

```bash
wget https://files.waveshare.com/upload/e/eb/Camera_overrides.tar.gz
tar zxvf Camera_overrides.tar.gz
sudo cp camera_overrides.isp /var/nvidia/nvcam/settings/
sudo chmod 664 /var/nvidia/nvcam/settings/camera_overrides.isp
sudo chown root:root /var/nvidia/nvcam/settings/camera_overrides.isp
```

---

## 6. IMX219 Supported Resolutions & Frame Rates

| Resolution  | FPS    | Notes               |
|-------------|--------|---------------------|
| 3264 x 2464 | ~21fps | Full sensor (8MP)   |
| 1920 x 1080 | ~47fps | 1080p crop          |
| 1640 x 1232 | ~41fps | 2x2 binning         |
| 1280 x 720  | ~90fps | 720p crop           |
| 640  x 480  | ~206fps| Quarter res         |

---

## 7. Available Overlays (reference)

| Overlay file                                          | Config                    |
|------------------------------------------------------|---------------------------|
| tegra234-p3767-camera-p3768-imx219-A.dtbo            | Single IMX219 on CAM0     |
| tegra234-p3767-camera-p3768-imx219-C.dtbo            | Single IMX219 on **CAM1** ← **ours** |
| tegra234-p3767-camera-p3768-imx219-dual.dtbo         | Dual IMX219 (CAM0 + CAM1) |
| tegra234-p3767-camera-p3768-imx219-imx477.dtbo       | IMX219 CAM0 + IMX477 CAM1 |
