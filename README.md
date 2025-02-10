# Shuttlefahrzeug
# 🚗 Lane Detection & Vehicle Control

## Overview
This project implements **lane detection** and **vehicle control** using computer vision and control algorithms. The system processes camera images to detect lanes, determine steering adjustments, and regulate speed based on road conditions and surrounding vehicles.

## 🏁 Lane Detection (5 Steps)

### 1️⃣ **Color Masking (`hlsDetector`)**
- Uses **HLS color space** (better contrast for white/yellow lanes).
- Separates **white & yellow lanes** based on lightness and saturation.
- Selects the dominant lane color dynamically.

### 2️⃣ **Edge Detection (`edgeDetector`)**
- **Canny Edge Detection** (default) or **Sobel** (configurable via `EDGEMODE`).
- **Gaussian Blur** for noise reduction.
- **Dilation** to enhance detected edges.

### 3️⃣ **Mask Combination**
- Performs a **logical AND operation** between **edge & color masks**.
- Filters out non-road elements (e.g., grass, sky).

### 4️⃣ **Region of Interest (ROI) Cropping**
- Removes **hood, sky, and side areas** using a polygonal mask.

### 5️⃣ **Lane Line Detection (`houghLineDetector`)**
- Uses **Hough Transform** to detect lane lines.
- Splits lines into **left & right lanes** based on slope.
- Uses **median filtering** for robust lane tracking.
- Implements **lane memory (`LANE_MEM_MAX`)** for smoother lane stabilization.

---

## 🎮 Vehicle Control

### **Lateral Control (`steeringControl`)**
- Calculates the **lane center** using detected lane lines.
- Uses a **PID controller** to adjust steering.
- Normalized within **[-1,1]** based on image width (580px).

### **Longitudinal Control (`accelerationControl`)**
#### 🚀 **Case 1: Cruise Control (CC)**
- Maintains a **target speed (`vel_t`)** when no obstacles are ahead.
- Uses a **PID controller** for speed regulation.
- Prevents rolling backward when speed < 2 km/h.

#### 🏎 **Case 2: Adaptive Cruise Control (ACC)**
- Activates when a **vehicle is detected within `DIST_ACT` meters**.
- Adjusts target distance dynamically based on speed.
- Uses **time-compensated PID control** for smooth acceleration.
- **Emergency braking** if distance < 5 meters.
- **Watchdog timer** deactivates ACC if no vehicle is detected.

---

## 📡 Communication
- **Sends `steer_norm` & `acc_norm`** via **TCP socket** to Tronis.
- Uses **bounding boxes (`processBox()`)** to detect nearby vehicles.



