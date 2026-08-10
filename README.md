# 🌲 The Walking Buddy

> **An open-source, subscription-free off-grid trail tracker & encrypted mesh communicator.**

![The Walking Buddy Banner](https://raw.githubusercontent.com/placeholder/the-walking-buddy/main/assets/banner.png)

[![Hack Club Forge](https://img.shields.io/badge/Hack_Club-Forge_Tier_2-orange?style=flat-square)](https://forge.hackclub.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](https://opensource.org/licenses/MIT)
[![ESP32-S3](https://img.shields.io/badge/MCU-ESP32--S3-blue?style=flat-square)](https://www.espressif.com/)
[![LoRa 868/915MHz](https://img.shields.io/badge/Radio-SX1262_LoRa-green?style=flat-square)](https://www.semtech.com/)

---

## 📌 Overview

**The Walking Buddy** is a rugged, handheld off-grid navigation device designed for hikers, backpackers, and outdoor enthusiasts. It solves the critical safety issue of lost cellular connection on trails without requiring expensive satellite subscriptions (like Garmin inReach). 

Using low-power **SX1262 LoRa mesh radios**, devices form a peer-to-peer network to share real-time GPS locations, directional vector maps, and encrypted text messages up to **10+ kilometers** away.

### ✨ Key Features
* **Zero Monthly Fees:** Runs on license-free 868MHz / 915MHz LoRa frequencies.
* **Group Join Codes:** Host or join private groups using 6-character encrypted channel keys (AES-128).
* **Tactical Radar Map:** Clean vector map rendering relative distance, bearing angle, and aliases of group members on a 1.3" IPS screen.
* **Phone-Free Operation:** 3-button physical navigation built for cold, wet, or gloved hands on the trail.
* **Long Battery Life:** 3000mAh battery delivers **20+ hours** of continuous active tracking.
* **Rugged Enclosure:** Multi-material 3D printed shell featuring an **Orange PETG** rigid core with **Black TPU** corner shock-absorbers.

---

## 🛠️ Hardware Specifications

| Component | Module / Part | Description |
| :--- | :--- | :--- |
| **Microcontroller** | ESP32-S3-WROOM-1U | Dual-Core 240MHz, Native USB, PSRAM |
| **LoRa Transceiver** | Semtech SX1262 | Long-Range Radio (868MHz EU / 915MHz US) |
| **GNSS / GPS** | ATGM336H / NEO-6M | High-sensitivity satellite positioning |
| **Display** | 1.3" SPI IPS (ST7789) | 240x240 Full-Color Screen |
| **Power System** | TP4056 + 3000mAh LiPo | USB-C charging with over-discharge protection |
| **Enclosure** | Bambu PETG + TPU | Custom shockproof 3D-printed case |

---

## 📐 Repository Structure

```text
├── assets/             # Screenshots, diagrams, and photos
├── CAD/                # 3D printable STL and STEP files (PETG body & TPU bumpers)
├── Electronics/        # EasyEDA / KiCad schematics and Gerber PCB files
├── Firmware/           # C++ ESP-IDF / Arduino C++ code for ESP32-S3
└── README.md           # Project documentation
