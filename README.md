# predictive-maintenance-monitor
Low-cost vibration anomaly detection system for SME predictive maintenance — Arduino Nano, MPU6050, I2C LCD
# Predictive Maintenance Vibration Monitor

**Self-funded hardware project | Liverpool, UK | 2026**

---

## The Problem

SME manufacturers lose significant revenue to unexpected machine failures. 
Enterprise predictive maintenance systems cost thousands. Small manufacturers 
run machines to failure because they cannot afford the alternative.

This project builds a working vibration anomaly detection system for under £50.

---

## What it does

- Mounts MPU6050 accelerometer on a running motor
- Learns the normal vibration signature during calibration
- Continuously monitors for deviation from baseline
- Identifies fault type from vibration pattern — bearing wear, imbalance, 
  looseness, or general degradation
- Displays real-time status on I2C LCD — NORMAL / WARNING / CRITICAL
- Logs all data to CSV via Python dashboard with live matplotlib graphs
- Operates standalone — no laptop required during deployment

---

## Hardware

| Component | Purpose | Cost |
|-----------|---------|------|
| Arduino Nano | Microcontroller | owned |
| MPU6050 GY-521 | Vibration sensing | £3 |
| TT Gearmotor 200RPM | Motor under test | £2 |
| L9110S Motor Driver | Motor control | £1 |
| I2C LCD 1602 | Status display | £8 |
| Breadboard + wires | Prototyping | £9 |

**Total: under £50**

---

## Repository contents

- `vibration_monitor.ino` — Arduino code, upload to Nano
- `vibration_dashboard.py` — Python real-time dashboard
- `WIRING_GUIDE.txt` — exact pin connections
- `CH340_DRIVER_FIX.md` — Windows 11 CH340 driver fix (Code 31)

---

## Connection to DATOM

This project directly addresses two unresolved problems from my MSc 
dissertation on modular robotic programmable matter:

1. Latching verification via vibration frequency signature
2. Actuator wear detection via anomaly monitoring

→ See: [DATOM Repository](https://github.com/Hrushimanju/DATOM)

---

## Status

🔧 Build in progress — wiring and calibration phase

---

*Part of an ongoing engineering portfolio. Built independently, self-funded.*
