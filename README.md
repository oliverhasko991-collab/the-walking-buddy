# 🥾 The Walking Buddy


**The Walking Buddy** is an open-source, off-grid GPS trail communicator and emergency beacon. It allows hikers to track each other, send messages, and trigger life-saving SOS alerts deep in the wilderness—all without needing cellular service or Wi-Fi.

Built as part of the **Hack Club Forge (Erebor Guild)**.

---

## 🏔️ What It Does

When you are out on a hike and lose cell phone service, your phone becomes a useless brick. The Walking Buddy solves this by using **LoRa (Long Range) Radio** to create a private, peer-to-peer mesh network between you and your group. 

### ✨ Core Features

* **📡 Off-Grid LoRa Communication:** Sends GPS coordinates and data packets up to several kilometers away using the 868MHz/915MHz radio band.
* **🚨 "PIEPS-Style" SOS Search & Rescue Mode:** 
  * Holding the dedicated red SOS button for 3 seconds locks the device into emergency mode.
  * It blasts a high-power LoRa distress signal.
  * Rescuers' devices freeze on an **"SOS ALERT"** screen and use a piezo buzzer to beep faster and faster as they physically get closer to the person in trouble.
* **📱 Bluetooth Phone Gateway:** Connects to your smartphone via BLE. If any person in the group gets a single bar of cell service, their phone can automatically relay the SOS coordinates to emergency services via SMS.
* **⛈️ Early Storm Warnings:** Uses an onboard BME280 sensor to track atmospheric pressure drops, warning the group of incoming bad weather before it hits.
* **🧭 Breadcrumb Backtracking:** Logs your GPS path to a MicroSD card every 30 seconds so you can retrace your steps in heavy fog.
* **📳 Haptic Feedback:** A built-in vibration motor alerts you to group messages even when the device is zipped inside your backpack.

---

## 🛠️ Hardware Architecture

The device is powered by an **ESP32-S3** microcontroller and runs off a single 3.7V 3000mAh flat LiPo battery. We specifically use an ESP32-S3 board with **built-in battery charging**, meaning you only need one USB-C port on the outside of the case to charge it and update code!

### Inside the Case:
1. **Brain:** ESP32-S3 (Dual-Core, WiFi/BLE)
2. **Radio:** SX1262 SPI LoRa Module
3. **Display:** 1.3" ST7789 IPS Color Screen (240x240)
4. **Navigation:** ATGM336H GPS Module
5. **Sensors:** BME280 (Temp/Pressure/Humidity)
6. **Alerts:** Active Piezo Buzzer & Coin Vibration Motor
7. **Storage:** SPI MicroSD Reader

> **Note:** For a full, itemized list of components and estimated costs, please see the `BOM.md` (Bill of Materials) file in this repository.

---

## 📂 Repository Structure

If you want to build your own Walking Buddy, everything you need is organized into these folders:

* 📁 **`/CAD`** — Contains the 3D printable `.stl` and `.step` files for the rugged PETG enclosure.
* 📁 **`/Electronics`** — Contains the wiring diagrams and custom PCB schematics.
* 📁 **`/Firmware`** — Contains the C++ source code to flash onto the ESP32-S3.
* 📁 **`/Assets`** — High-resolution photos, 3D renders, and UI screenshots.

---

## 💻 Software Overview

The firmware is written in **C++** using PlatformIO / Arduino IDE. Here is a quick look at how the SOS logic is handled in the code:

```cpp
// Example logic for the PIEPS-style SOS distance beeper
void handleSOSBeep(float distanceToTarget) {
  if (distanceToTarget > 500.0) {
    // Far away: Slow beep every 2 seconds
    triggerBuzzer(100, 2000); 
  } else if (distanceToTarget <= 500.0 && distanceToTarget > 50.0) {
    // Getting closer: Medium beep every 1 second
    triggerBuzzer(100, 1000);
  } else {
    // Very close (<50m): Rapid continuous beeping
    triggerBuzzer(100, 250);
  }
}
