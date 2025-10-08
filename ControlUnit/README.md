# 🚴‍♂️ Control Unit (Stationary Rig)

The Control Unit is the **human interface station** that reads all rider inputs and sends real-time commands to the Vehicle Unit over **ESP-NOW**.  
It simulates telepresence driving: pedaling maps to robot speed, steering to direction, and braking to deceleration or stop.

---

## ⚙️ Core Functions

- Capture pedal cadence, steering, and brake inputs
- Transmit control packets (low-latency)
- Receive vehicle telemetry (IMU, bump)
- Drive bump actuators to simulate road feedback
- Provide UI feedback (display, LEDs, buzzer)
- Ensure safety with local E-STOP

---

## 🧩 Hardware Components

| # | Component | Type | Function / Notes | Connection |
|---|------------|------|------------------|-------------|
| 1 | **ESP32-S3 Dev Board** | MCU / Comms | Main control logic, ESP-NOW link | SPI, I²C, GPIO |
| 2 | **Crank Encoder — AS5048A** | Magnetic SPI | Measures crank angle and cadence | SPI |
| 3 | **Pedal Load Cell + HX711** | Strain gauge ADC | Measures pedal force / torque | HX711 data pins |
| 4 | **Steering Encoder — AS5600 / EC11** | Magnetic / rotary | Captures handlebar rotation | I²C or GPIO |
| 5 | **Brake Lever Sensor** | Microswitch / Hall | Brake input | GPIO / ADC |
| 6 | **Reverse Button** | Momentary button | Reverse mode toggle | GPIO |
| 7 | **Bump Actuators (front & rear)** | Micro servos / linear actuators | Simulate road bumps using IMU telemetry | PWM |
| 8 | **UI Display (OLED / TFT)** | SSD1306 / ST7735 | Shows connection, speed, mode | I²C / SPI |
| 9 | **Buzzer & LEDs** | Digital outputs | Provide collision & status alerts | GPIO |
| 10 | **Power & E-STOP** | Local PSU + safety switch | Protect user & hardware | Relay or power cut line |

---

## 🔌 Wiring Overview

| Signal | Interface | Example ESP32 Pin |
|---------|------------|------------------|
| Crank Encoder (AS5048A SPI) | SPI | 5 / 18 / 19 / 23 |
| HX711 Load Cell | GPIO | 32 / 33 |
| Steering Encoder (AS5600 I²C) | I²C | 21 / 22 |
| Brake Switch | GPIO | 14 |
| Reverse Button | GPIO | 13 |
| Servo (Front Bump) | PWM | 25 |
| Servo (Rear Bump) | PWM | 26 |
| OLED (SSD1306) | I²C | 21 / 22 |
| Buzzer | GPIO | 27 |
| E-STOP | GPIO | 4 |

---

## 🧮 Control Flow

1. **Input Reading**
   - Crank encoder → cadence & direction  
   - Steering encoder → steering angle  
   - Brake / reverse → mode bits  
   - (Optional) load cell → force input

2. **Command Packet Assembly**
   - Builds control struct: `{speed, steering, brake, reverse, mode}`  
   - Transmitted to Vehicle Unit via ESP-NOW at ~30 Hz

3. **Telemetry Handling**
   - Receives `{pitch, roll, accelZ, bump, current}`  
   - Filters and maps to servo actuation to simulate motion feedback

4. **UI Display**
   - Displays live telemetry, connection strength, mode, and safety status

---

## 💡 Firmware Tips

- **Prioritize real-time inputs** — SPI and interrupts for encoders.
- **Separate loops**: input reading (high rate), telemetry (medium rate), UI (low rate).
- **Filter IMU bump data** with simple moving average before servo mapping.
- **Use watchdog** to detect lost link (disable servos and freeze state).

---

## 🧩 Future Upgrades

- Add haptic motor or resistance control for realistic pedaling feedback.
- Implement OLED UI menu navigation.
- Integrate Bluetooth link for monitoring via mobile app.

---

## 📜 License

MIT License © 2025 Napoleon P. Visitacion
