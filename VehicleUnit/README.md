# 🤖 Vehicle Unit (Robot Avatar)

The Vehicle Unit is the **mobile robot** that acts as the physical “avatar” of the stationary rider.  
It mirrors the rider’s pedaling speed, steering, and braking inputs in real time using **ESP-NOW** communication from the Control Unit.

---

## ⚙️ Core Functions

- Receive control packets (speed, steering, brake, reverse)
- Execute motor velocity and steering control
- Monitor IMU orientation and bump switches
- Report telemetry (IMU, current, collisions) back to Control Unit
- Implement safety shutdown on E-STOP or fault flag

---

## 🧩 Hardware Components

| # | Component | Type | Purpose / Notes | Connection |
|---|------------|------|----------------|-------------|
| 1 | **ESP32-S3 Dev Board** | MCU / Communication | Main controller for all logic, motor control, and telemetry | SPI, I²C, PWM, GPIO |
| 2 | **DC Drive Motors (×2)** | Brushed DC motors | Main drive; torque and motion | Motor driver outputs |
| 3 | **Motor Driver** (TB67H420FTG / VNH2SP30) | Dual H-bridge | Drives both motors from PWM and DIR | PWM + DIR pins from ESP32 |
| 4 | **Wheel Encoders** (AS5048A / quadrature) | Magnetic / optical | Feedback for wheel speed and direction | SPI or GPIO interrupts |
| 5 | **IMU — ICM-42688-P** | 6-axis accel + gyro | Orientation, pitch, roll, bump detection | SPI |
| 6 | **Bump Sensors (×2–4)** | Mechanical microswitch | Collision detection | GPIO inputs w/ interrupt |
| 7 | **Current Sensor — INA226** | I²C | Detect stall / load | I²C bus |
| 8 | **Steering Servo (optional)** | PWM servo | Controls steering angle | PWM output |
| 9 | **Power System** | Battery + E-STOP | Supplies robot and motor power | Wired with safety relay |

---

## 🔌 Wiring Summary

| Signal | Interface | Example ESP32 Pin |
|---------|------------|------------------|
| Motor A PWM | GPIO | 25 |
| Motor B PWM | GPIO | 26 |
| Encoder A (left) | Interrupt | 34 |
| Encoder B (right) | Interrupt | 35 |
| IMU SPI (CS / CLK / MISO / MOSI) | SPI | 5 / 18 / 19 / 23 |
| INA226 (I²C SDA/SCL) | I²C | 21 / 22 |
| Bump sensors | GPIO | 4 / 15 |
| Steering Servo | PWM | 27 |
| E-STOP input | GPIO | 14 |

---

## 🧮 Control Flow

1. **ESP-NOW Reception**  
   - Parses control struct: `{speed, steering, brake, reverse, mode}`

2. **Motion Execution**  
   - Uses wheel encoders + PID to maintain commanded velocity  
   - Applies differential steering or servo steering

3. **Telemetry Feedback**  
   - Reads IMU + current + bump status  
   - Sends packet `{pitch, roll, accelZ, bump, current, fault}` to Control Unit

4. **Safety**  
   - Monitors E-STOP GPIO  
   - Cuts PWM to zero on overcurrent, bump, or lost link

---

## ⚡ Power Design Notes

- Use a **7.4–12V Li-ion pack** for motors.
- Separate **5V regulator** for ESP32 and sensors.
- Always connect **common ground**.
- E-STOP switch cuts motor driver VIN and triggers GPIO interrupt.

---

## 🧠 Firmware Notes

- Motor control loop: 50–100 Hz
- IMU sampling: 200 Hz (filtered)
- ESP-NOW send/receive interval: 20–33 ms
- Recommended libraries:  
  - `esp_now.h`  
  - `PID_v1.h`  
  - `Adafruit_ICM42688.h`  
  - `Wire.h` / `SPI.h`

---

## 🧩 Future Enhancements

- Add camera-based FPV or Walksnail system mount.
- Integrate PID auto-tuning for terrain adaptation.
- Add ultrasonic / TOF sensors for collision pre-warning.

---

## 📜 License

MIT License © 2025 Napoleon P. Visitacion
