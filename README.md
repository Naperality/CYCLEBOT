# 🚴‍♂️ Cyclebot Maze Runner

This project is a **telepresence mechatronics system** that links a **stationary control rig (bicycle)** to a **remote vehicle unit (robot avatar)** using **ESP-NOW wireless communication**.  
The system allows a user to pedal, steer, and brake on the stationary rig while a mobile robot mirrors these movements in real-time.

---

## 🧩 System Overview

### 🔸 Control Unit (Stationary Rig)
The Control Unit acts as the **human interface** — capturing pedaling cadence, steering, braking, and mode controls.  
It transmits real-time control packets via ESP-NOW to the Vehicle Unit and receives feedback such as IMU data for tactile simulation.

### 🔸 Vehicle Unit (Robot Avatar)
The Vehicle Unit is the **mobile robot** that receives control signals and performs actual movement.  
It handles motor control, wheel feedback (encoders), and sends telemetry such as tilt, bumps, and load conditions back to the Control Unit.

---

## 📡 Communication Architecture

- **Protocol:** ESP-NOW (low-latency peer-to-peer)
- **Control loop:** ~30 Hz (33 ms)
- **Telemetry feedback:** ~20–50 Hz
- **Direction:**  
  - Control → Vehicle: speed, steering, brake, reverse, mode  
  - Vehicle → Control: IMU data, bump events, fault flags

---

## ⚙️ Hardware Summary

| Subsystem | MCU | Main Sensors | Actuators | Notes |
|------------|-----|---------------|------------|-------|
| Control Unit | ESP32-S3 | AS5048A (crank), AS5600 (steering), brake switch, HX711 (load cell) | Servos for bump feedback | Stationary bicycle rig |
| Vehicle Unit | ESP32-S3 | IMU (ICM-42688-P), wheel encoders, INA226 (current), bump switches | DC motors + steering servo | Mobile robot avatar |

---

## 🧠 Core Functions

- Real-time control translation (cadence → velocity)
- Closed-loop motor PID with encoder feedback
- Telemetry transmission (IMU, bump, current)
- Simulated haptic bump feedback on rig
- Safety features (E-STOP, stall detection)

---

## 📁 Repository Structure

```
CYCLEBOT/
│
├── ControlUnit/
│ ├── README.md # Details of the Control Unit hardware and firmware
│ ├── src/ # Arduino/ESP32 code
│ └── hardware/ # Schematics, CAD files
│
├── VehicleUnit/
│ ├── README.md # Details of the Vehicle Unit hardware and control code
│ ├── src/ # Raspberry Pi control scripts
│ └── hardware/ # Wiring diagrams, chassis files
│
└── README.md # (This file) Project overview
```

---

## 🧩 Recommended Tools

- **Microcontroller:** ESP32-S3 (e.g., ESP32-S3-N16R8 DevKitC)
- **Motor driver:** TB67H420FTG or VNH2SP30
- **IMU:** ICM-42688-P (SPI)
- **Encoders:** AS5048A / AS5600 (SPI / I²C)
- **Communication library:** `esp_now.h`
- **PID & filtering:** `PID_v1`, `SimpleKalmanFilter` (optional)

---

## 🔒 Safety Notes

- Always test with **E-STOP enabled** on both units.
- Keep **motor power and logic power isolated** but share a common ground.
- Validate **encoder and IMU orientation** before live operation.

---

## 📜 License

This project is released under the **MIT License** — open for modification and academic use.

---

## 👤 Author

**Napoleon P. Visitacion**  
Computer Engineering Project — 2025  x
Powered by ESP32-S3 and passion for robotics.