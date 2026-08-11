# 🥾 The Walking Buddy


**The Walking Buddy** is an open-source, off-grid mesh trail communicator, group tracker, and emergency SOS beacon designed for outdoor exploration. It allows hiking groups to track each other's live coordinates, send group alerts, monitor weather conditions, and trigger search-and-rescue beacons deep in the wilderness—**completely without cellular service or Wi-Fi**.

Built as part of the **Hack Club Forge (Erebor Guild)**.

---

## 🏔️ Why It Exists & How It Works With Friends

When hiking in remote mountains or dense forests, cellular coverage drops to zero, making it easy for groups to get separated. 

**The Walking Buddy** connects everyone in your group via a private **LoRa (Long Range) Radio Mesh Network**. Each hiker carries a Buddy unit. The units silently exchange GPS locations and system status in the background every few seconds, allowing you to see where your friends are on your screen, estimate their distance from you, and stay connected at all times.

---

## ✨ Complete Feature Breakdown

### 🛰️ Group Hiking & Mesh Tracking
* **Real-Time Group Radar:** See direction arrows and distances to each friend in your hiking group right on the 1.3" color screen.
* **Peer-to-Peer Mesh Networking:** Devices automatically relay messages through each other, extending the total group range up to several kilometers over mountain ridges.
* **Off-Grid Group Ping:** Send quick haptic alerts ("Wait up!", "Taking a break", "Need assistance") to the entire group without needing phone service.

### 🔋 Battery Life & Power Performance
Powered by a single **3.7V 3000mAh flat LiPo battery** running an optimized ESP32-S3 power profile:

* **Active Trail Tracking (Continuous GPS + LoRa Mesh + Screen On):** **20 – 25 Hours** *(More than enough for a full multi-day backcountry trip).*
* **Eco / Saver Mode (Screen Off, GPS/LoRa Ping every 60s):** **Up to 3 – 5 Days**.
* **Single USB-C Charging & Data:** Charge the internal battery and flash new code updates through a single external USB-C port on the case.

### 🚨 "PIEPS-Style" Search & Rescue Emergency Mode
* **Guarded 3-Second Hold:** Holding the dedicated red SOS button for 3 seconds locks the unit into maximum-power emergency broadcast mode.
* **Variable-Rate Audio Beeper:** Rescuers' units lock onto the distress signal. The built-in piezo buzzer beeps faster as a rescuer physically gets closer to the missing hiker:
  * *Far away (>100m):* Slow pulse (1 beep / 2 seconds)
  * *Getting closer (20m - 100m):* Medium pulse (1 beep / second)
  * *Immediate vicinity (<20m):* Rapid continuous chirping
* **BLE Phone Relay Gateway:** If any single hiker in the group hits a ridge with even one bar of cellular coverage, their phone connects via Bluetooth (BLE) to pass the emergency GPS coordinates out to emergency services via SMS.

### 🌦️ Navigation & Environmental Monitoring
* **Early Storm Warnings:** An onboard **BME280 sensor** continuously measures barometric pressure drops, warning your group of incoming mountain storms before they hit.
* **MicroSD Breadcrumb Logging:** Automatically records your GPS trail coordinates to a MicroSD card every 30 seconds so you can safely retrace your steps in fog or nightfall.
* **Haptic Vibration Alerts:** Integrated coin vibration motor alerts you to group messages or storm warnings even when the device is stowed in a backpack pocket.

---

## 🛠️ Hardware Specifications

| Component | Part Selection | Function |
| :--- | :--- | :--- |
| **Brain** | ESP32-S3 (N16R8) | Microcontroller with Wi-Fi, BLE, and integrated LiPo charging |
| **Radio** | SX1262 LoRa Transceiver | 868MHz / 915MHz long-range mesh radio communications |
| **GPS** | ATGM336H GNSS Module | High-precision position tracking with active ceramic patch antenna |
| **Display** | 1.3" ST7789 IPS Display | $240 \times 240$ high-brightness sunlight-readable screen |
| **Sensors** | BME280 | Temperature, humidity, barometric pressure, and altitude |
| **Alerts** | Active Piezo + Vibration Motor | Multi-sensory audio/haptic feedback for alerts & SOS tracking |
| **Power** | 3.7V 3000mAh Flat LiPo | ~25hr active battery runtime in a compact $8\text{mm}$ pouch |
| **Enclosure**| 3D-Printed PETG Case | Rugged, $28\text{mm}$ thick drop-resistant enclosure |

---

## ⚠️ Prototype Status & Disclaimer

> **CRITICAL NOTICE:**  
> This repository contains **experimental prototype firmware and untested electronic schematics** created for educational purposes as part of Hack Club Forge. 
> 
> This hardware and software **HAS NOT BEEN FIELD TESTED** or certified by emergency response services. **DO NOT rely on this device as your sole emergency signaling tool or life-safety apparatus in hazardous environments.** Always carry a dedicated commercial PLB (Personal Locator Beacon) or satellite communicator when venturing into backcountry wilderness.

---

## 📂 Repository Layout

```text
├── CAD/            # 3D printable STL and STEP files for the PETG enclosure
├── Electronics/    # Schematics, pinout tables, and PCB layout documentation
├── Firmware/       # C++ ESP32-S3 source code and PlatformIO configurations
├── Assets/         # Project images, UI designs, and render graphics
├── README.md       # Project overview
└── LICENSE         # MIT Open Source License
