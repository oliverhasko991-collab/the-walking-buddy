

> ⚠️ **CRITICAL DISCLAIMER & WARNING**
> **THIS IS AN UNTESTED PROTOTYPE / CONCEPTUAL SCHEMATIC.**
> This circuit configuration has **NOT** been fabricated, breadboard-tested, or verified on physical hardware. Component pin assignments may change during layout validation. Do not order custom PCBs based on this draft without breadboard verification!

---

# 🔌 Electrical Schematics & Pinout Guide (Prototype v0.1)

> **Status:** Draft / Untested Concept
> **Microcontroller target:** ESP32-S3-WROOM-1 / ESP32-S3 DevKit

---

## 1. ESP32-S3 Pinout Assignment Table

All peripheral connections are mapped to the ESP32-S3 GPIO pins below. Note that SPI devices (SX1262, ST7789 Display, MicroSD) share the primary SPI bus (`SCK`, `MOSI`, `MISO`) with dedicated Chip Select (`CS`) pins.

| Peripheral | Module Pin | ESP32-S3 Pin | Signal / Function | Notes |
| --- | --- | --- | --- | --- |
| **SX1262 LoRa** | `NSS` (CS) | **GPIO 10** | SPI Chip Select | Hardware SPI |
|  | `SCK` | **GPIO 12** | SPI Clock | Shared SPI Bus |
|  | `MOSI` | **GPIO 11** | SPI Data Out | Shared SPI Bus |
|  | `MISO` | **GPIO 13** | SPI Data In | Shared SPI Bus |
|  | `DIO1` | **GPIO 14** | Radio Interrupt | Active High |
|  | `BUSY` | **GPIO 15** | Radio Busy State | Active High |
|  | `NRST` | **GPIO 9** | Module Reset | Active Low |
| **ST7789 Screen** | `VCC` | **3.3V Rail** | Power Supply | Needs regulated 3.3V |
|  | `GND` | **GND Rail** | Ground | Common Ground |
|  | `SCL` (SCK) | **GPIO 12** | SPI Clock | Shared SPI Bus |
|  | `SDA` (MOSI) | **GPIO 11** | SPI Data Out | Shared SPI Bus |
|  | `CS` | **GPIO 42** | Display CS | Dedicated CS Line |
|  | `DC` | **GPIO 45** | Data / Command | Control Toggle |
|  | `RES` | **GPIO 21** | Screen Reset | Hardware Reset |
|  | `BLK` | **GPIO 41** | Backlight Control | Optional PWM Dimming |
| **MicroSD Slot** | `CS` | **GPIO 40** | SD Card CS | Dedicated CS Line |
|  | `SCK` | **GPIO 12** | SPI Clock | Shared SPI Bus |
|  | `MOSI` | **GPIO 11** | SPI Data Out | Shared SPI Bus |
|  | `MISO` | **GPIO 13** | SPI Data In | Shared SPI Bus |
| **GPS Module** | `TX` | **GPIO 18** | ESP32 UART RX1 | Hardware Serial1 |
|  | `RX` | **GPIO 17** | ESP32 UART TX1 | Hardware Serial1 |
| **BME280 Sensor** | `SDA` | **GPIO 1** | I2C Data Line | Requires 4.7k Pullup |
|  | `SCL` | **GPIO 2** | I2C Clock Line | Requires 4.7k Pullup |
| **Inputs & Alerts** | `SOS Button` | **GPIO 4** | Emergency Trigger | Internal Pullup, Active LOW |
|  | `Nav Up` | **GPIO 5** | Menu Navigation | Internal Pullup, Active LOW |
|  | `Nav Down` | **GPIO 6** | Menu Navigation | Internal Pullup, Active LOW |
|  | `Nav Select` | **GPIO 7** | Menu Enter | Internal Pullup, Active LOW |
|  | `Buzzer` | **GPIO 16** | Active Piezo Siren | Driven via NPN Transistor |
|  | `Vibe Motor` | **GPIO 8** | Haptic Motor | Driven via NPN Transistor |

---

## 2. System Architecture & Block Diagram

```text
               ┌─────────────────────────────────────────┐
               │         3.7V 3000mAh LiPo Battery        │
               └────────────────────┬────────────────────┘
                                    │
                                    ▼
               ┌─────────────────────────────────────────┐
               │    TP4056 Charger / Power Switch        │
               └────────────────────┬────────────────────┘
                                    │ (3.3V / VBUS)
                                    ▼
    ┌───────────────────────────────────────────────────────────────┐
    │                        ESP32-S3 MCU                           │
    │                                                               │
    │   [I2C: GPIO 1, 2]         [UART1: GPIO 17, 18]              │
    │          │                         │                          │
    │          ▼                         ▼                          │
    │   ┌──────────────┐         ┌──────────────┐                   │
    │   │ BME280 Sensor│         │ ATGM336H GPS │                   │
    │   └──────────────┘         └──────────────┘                   │
    │                                                               │
    │   [Shared SPI Bus: GPIO 11 (MOSI), 12 (SCK), 13 (MISO)]      │
    │          │                         │                │         │
    │          ├─────────────────────────┼────────────────┤         │
    │          ▼                         ▼                ▼         │
    │   ┌──────────────┐         ┌──────────────┐ ┌──────────────┐  │
    │   │ SX1262 LoRa  │         │ ST7789 Display│ │ MicroSD Slot │  │
    │   │ (CS: GPIO 10)│         │ (CS: GPIO 42)│ │ (CS: GPIO 40)│  │
    │   └──────────────┘         └──────────────┘ └──────────────┘  │
    │                                                               │
    │   [GPIO Inputs & Outputs]                                     │
    │          │                                                    │
    │          ├─────► SOS Button (GPIO 4 - Guarded)                │
    │          ├─────► Menu Buttons (GPIO 5, 6, 7)                  │
    │          ├─────► Piezo Buzzer (GPIO 16 -> NPN Driver)         │
    │          └─────► Vibration Motor (GPIO 8 -> NPN Driver)       │
    └───────────────────────────────────────────────────────────────┘

```

---

## 3. Power & Driver Circuit Notes

1. **Vibration Motor & Buzzer Driver Circuits:**
Do **not** connect the vibration motor or active piezo buzzer directly to ESP32 GPIO pins, as they can draw more current than the pin can safely output.
* Use a standard **2N2222 or S8050 NPN transistor** for both drivers.
* Place a **1N4001 flyback diode** across the vibration motor terminals to absorb inductive voltage spikes.
* Place a **1kΩ resistor** between the GPIO pin and the base of the transistor.


2. **I2C Pullup Resistors:**
* The BME280 pressure sensor requires **4.7kΩ pull-up resistors** connected from `SDA` (GPIO 1) and `SCL` (GPIO 2) to the `3.3V` rail if the module board doesn't already include them.


3. **Power Bus & Battery Switch:**
* Place a small SPDT slide switch on the positive line between the battery's positive lead and the board input to completely kill system power when packed away.



---

