# EmberWatch Drone — CAD Assembly Models

Approximate-dimension STL models for visual assembly layout. Not exact CAD — good enough to check component placement, stack height, and cable routing before physical build.

All dimensions in **mm**.

---

## Files

| File | Component | Key dimensions |
|---|---|---|
| `01_frame_GEPRC_Mark4_7inch.stl` | Frame (plates + arms + motor pads) | 295 mm wheelbase |
| `02_battery_CNHL_1500mAh_6S.stl` | 1500mAh 6S LiPo | 135×45×35 mm |
| `03_jetson_orin_nano_devkit.stl` | Jetson Orin Nano 8GB devkit | 100×79×34 mm |
| `04_motor_Xrotor_2807.stl` | Xrotor 2807 1300KV motor ×4 | 35 mm dia, 34 mm tall |
| `05_prop_HQProp_7_5x3.stl` | HQProp 7.5×3×3 propeller ×4 | 190.5 mm dia |
| `06_esc_Tekko32_F4_4in1.stl` | Tekko32 F4 4-in-1 50A ESC | 40×40×8 mm |
| `07_fc_Kakute_H7.stl` | Holybro Kakute H7 v1.5 FC | 36×36×7 mm |
| `08_gimbal_Skydroid_C10Pro.stl` | Skydroid C10 Pro 3-axis gimbal | 75×55×70 mm |
| `09_camera_IMX219_160.stl` | Waveshare IMX219-160 camera | 32×32×14 mm |
| `10_rangefinder_VL53L1X.stl` | TOF400C VL53L1X rangefinder | 25×16×6 mm |

To regenerate: `python3 generate_models.py`

---

## 3D Printed Parts

| File | Part | Key dimensions |
|---|---|---|
| `print_01_landing_leg.stl` | Tall landing gear leg ×4 | 14×14mm post, 65mm tall, 40mm foot |
| `print_02_jetson_mount.stl` | Jetson Orin Nano top-plate mount | 100×79×4mm + standoff bosses |
| `print_03_gimbal_mount_bracket.stl` | Belly gimbal mount (Skydroid C10 Pro) | 80×55×40mm L-bracket |
| `print_04_bec_tray.stl` | UBEC Duo tray | 56×36mm outer, 10mm walls |
| `print_05_fc_spacer_tpu.stl` | TPU anti-vibration FC spacer ×4 | 8×8×3mm |
| `print_06_rangefinder_bracket.stl` | VL53L1X rangefinder bracket | 30×20mm L-bracket |
| `print_07_camera_bracket.stl` | IMX219 forward camera bracket | 40×40mm angled mount |
| `print_08_traffic_cone.stl` | Flight zone marker cone ×4 | 150mm tall, 120mm base dia |

---

## Viewing in FreeCAD (recommended)

[Download FreeCAD](https://www.freecad.org/downloads.php) — free, works on Linux/Windows/macOS.

### 1 — Open all models as a multi-document assembly

1. Launch FreeCAD
2. **File → Open** → select all 10 `.stl` files at once (Ctrl+click or Shift+click)
3. FreeCAD opens each as a separate document

### 2 — Merge into one scene

1. With the first STL open, go to **File → Merge project…**
2. Select the next STL file → click **Open**
3. Repeat for all remaining files
4. All meshes now appear in the same **Model tree** on the left

### 3 — Position components (no constraints needed)

1. Click a mesh in the **Model tree** or the 3D view to select it
2. Go to **Part menu → Mirror** (ignore) — instead use the **Placement** property:
   - In the **Model tree**, click the mesh
   - Bottom-left panel shows **Properties → Data tab → Placement**
   - Expand **Placement → Position** and set **x / y / z** to move it
   - Expand **Placement → Axis + Angle** to rotate it
3. Alternatively: select the mesh → press **Space** to toggle visibility, or drag in the 3D view using the **Transform** tool (**Part → Transform**)

### 4 — Suggested assembly layout (starting positions)

Use these as a starting point, then adjust by eye:

| Component | Suggested position (x, y, z mm) | Notes |
|---|---|---|
| Frame | 0, 0, 0 | Origin — everything referenced to this |
| ESC | 0, 0, 5 | Sits on bottom plate, centre stack |
| FC | 0, 0, 18 | On top of ESC with standoffs |
| Jetson | 0, 0, 45 | On top of FC stack via standoffs |
| Motor FL | −104, 104, 5 | Front-left arm tip |
| Motor FR | 104, 104, 5 | Front-right arm tip |
| Motor RL | −104, −104, 5 | Rear-left arm tip |
| Motor RR | 104, −104, 5 | Rear-right arm tip |
| Prop FL | −104, 104, 39 | On motor shaft |
| Prop FR | 104, 104, 39 | On motor shaft |
| Prop RL | −104, −104, 39 | On motor shaft |
| Prop RR | 104, −104, 39 | On motor shaft |
| Battery | 0, 0, −20 | Strapped under bottom plate |
| Gimbal | 0, 0, −55 | Under bottom plate, centred |
| Camera | 0, 105, 50 | Front of top plate, forward-facing |
| Rangefinder | 0, 0, −10 | Bottom plate underside, pointing down |

### 5 — Navigation in 3D view

| Action | Mouse |
|---|---|
| Rotate view | Middle-mouse drag |
| Pan | Shift + middle-mouse drag |
| Zoom | Scroll wheel |
| Fit all | Press **V** then **F** |
| Select | Left-click |

### 6 — Export as single assembly (optional)

1. Select all meshes in the Model tree (Ctrl+A)
2. **Part → Boolean → Union** (if you want one merged mesh)
3. **File → Export** → choose `.stl` or `.obj`

---

## Alternative: Blender

If you prefer Blender (also free, [blender.org](https://www.blender.org)):

1. **File → Import → STL** — repeat for each file
2. Each import adds a new object to the scene
3. Select an object → press **G** to grab/move, **R** to rotate, **S** to scale
4. Press **G X/Y/Z** + type a number to move along an axis by exact mm (e.g. `G Z 45`)
5. Use **Numpad 1/3/7** for front/side/top orthographic views
