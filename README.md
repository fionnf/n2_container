 # Sealed Enclosure for Electrochemical Cycling

## Overview

This repository contains the Arduino control and monitoring software for a fully sealed, oxygen-free cycling device designed for in-situ studies of air-sensitive redox flow battery (RFB) chemistries. The system integrates **pump control**, **environmental monitoring**, and **data logging** to maintain stable experimental conditions while circulating electrolyte through a **flow NMR system**.

### System Components

The system consists of a **modified 1.9 L polypropylene container** reinforced with a metal clamping system to maintain integrity under **0.3 bar nitrogen overpressure**. The enclosure is outfitted with epoxy-sealed feedthroughs for:

- **Electrical connections**: 12 wires for charge/discharge cycling, pump control, and sensors.
- **Fluidic connections**: HPLC-grade **PEEK and PTFE tubing** for electrolyte circulation.
- **Environmental monitoring**: **Oxygen, temperature, and humidity sensors** track internal conditions.
- **Nitrogen inlets and outlets**: Ensures stable **oxygen-free conditions** via an external nitrogen supply.

The enclosure houses **electrolyte reservoirs (20 mL each), a pump assembly, and circulation tubing**. A nitrogen outlet **bubbler** prevents backflow, minimizing oxygen contamination over extended cycling periods.

### Microcontroller & Flow Control

An **Arduino Beetle (Leonardo-based)** microcontroller regulates electrolyte circulation and monitors environmental conditions inside the sealed enclosure. The **three KNF FF12 diaphragm pumps** are controlled via **PWM signals**, allowing precise control over flow rates.

- **Pump Speed Matching**:  
  - One pump is designated as the **boss pump**, maintaining a fixed flow rate.  
  - The second pump dynamically adjusts to match its **RPM using a feedback loop**.
- **Flow Rates**:  
  - Electrolyte circulation through the **battery: 20 mL/min**  
  - Electrolyte circulation through the **NMR loop: 3 mL/min**  
- **Manual Adjustment**:  
  - Pumps can be controlled in real-time via **serial input**.
- **Automatic Compensation**:  
  - Software adjusts for flow variations, ensuring stable circulation throughout **long-term cycling** and **flow NMR experiments**.

---

## Repository Contents

### [`Pump_control.ino`](Pump_control.ino)

This script controls and monitors the **three membrane diaphragm pumps**, ensuring constant electrolyte flow rates and allowing real-time manual control.

#### Features:
- **PWM Control**: Adjusts pump speeds (Pins **D9, D10, D11**).
- **Tachometer Feedback**: Monitors RPM via **sensors on A0, A1, A2**.
- **Adaptive Flow Control**: One pump operates at a fixed flow rate, while the second adjusts to match.
- **Serial Input for Flow Adjustment**: Allows real-time user-defined flow control.
- **Data Logging**: Saves pump speeds to an SD card for tracking.

---

### [`Sensor_console.ino`](Sensor_console.ino)

This script **monitors environmental conditions** inside the sealed enclosure, logging **oxygen, humidity, and temperature** data while displaying real-time values on an LCD.

#### Features:
- **Environmental Sensors**:  
  - **Oxygen sensor** (EZO O2)  
  - **Humidity/Temperature sensor** (EZO HUM)  
- **LCD Display (I2C 0x27, 16x2)**:  
  - **Humidity (%)** and **oxygen levels (ppt)**  
- **Data Logging**:
  - **SD Card (SPI)** stores real-time sensor data.
  - **RTC (Real-Time Clock)** timestamps each entry.
- **Serial Output**: Sensor values printed for remote monitoring.

---

## Data Logging & NMR Integration

- Sensor readings and pump data are logged to an **SD card** with timestamps for full traceability.
- The system ensures that **flow NMR experiments** remain stable under nitrogen overpressure.
- Spectra are acquired using **VNMRJ** via **Bash scripting**, with **automated filename timestamps**.

### **Flow NMR Setup**
- Experiments conducted on a **Varian 400 MHz spectrometer**.
- Electrolyte is circulated through a **Bruker flow tube**, optimized using **gradient shimming**.
- A **fluorobenzene capillary standard** (0.5M C₆H₅F in MeCN) ensures **internal referencing**.

---

## Getting Started

### **1. Hardware Setup**
- Assemble the enclosure **inside a nitrogen-filled glovebox**.
- Connect:
  - **Arduino Beetle** to pumps and sensors.
  - **LCD Display** (I2C, address **0x27**).
  - **SD Card module** (SPI).
  - **RTC module** for timestamping.
- Ensure the **nitrogen supply is connected** before transferring the enclosure out.

### **2. Software Installation**
1. Clone this repository:  
   ```bash
   git clone https://github.com/fionnf/n2_container.git
   ```
2. Open Arduino IDE and install required libraries:
	- Ezo_i2c (Atlas Scientific sensor library)
	- Wire.h (I2C communication)
	- LiquidCrystal_I2C.h (LCD control)
	- SPI.h (SD card logging)
	- SD.h (File storage)
	- RTClib.h (Real-time clock)
3.	Upload Pump_control.ino and Sensor_console.ino to the Arduino Beetle.

### **3. Running the System**
Monitor the Serial Output (9600 baud): '''Pump 1 RPM, Pump 1 Avg, Pump 2 RPM, Pump 2 Avg, NMR Pump RPM, NMR Pump Avg'''
Adjust pump speeds via serial input: '''Pump 1 RPM, Pump 2 RPM, NMR Pump RPM'''
Check sensor values on the LCD display: '''Humidity (%), Oxygen (ppt)'''

## Lisence
This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.
## Acknowledgments
- Prof. Dr. Edwin Otten for guidance and support.