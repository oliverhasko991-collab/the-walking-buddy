/*
 * ============================================================================
 * PROJECT: The Walking Buddy (Firmware Prototype v0.1)
 * DESCRIPTION: ESP32-S3 Off-Grid LoRa Communicator with PIEPS SOS Mode
 * 
 * ⚠️ WARNING: EXPERIMENTAL PROTOTYPE CODE FOR DEMONSTRATION ONLY.
 * THIS CODE HAS NOT BEEN FIELD-TESTED. DO NOT RELY ON IT FOR REAL EMERGENCIES.
 * ============================================================================
 */

#include <Arduino.h>
#include <SPI.h>
#include <RadioLib.h>      // RadioLib for SX1262 LoRa Transceiver
#include <TinyGPS++.h>     // TinyGPSPlus for GNSS parsing

// ----------------------------------------------------------------------------
// PIN DEFINITIONS (Adjust to match your ESP32-S3 board layout)
// ----------------------------------------------------------------------------
#define PIN_SOS_BUTTON   4   // Active-LOW hardware push-button
#define PIN_BUZZER       5   // Active Piezo Buzzer pin
#define PIN_VIBE_MOTOR   6   // Haptic Vibration Motor pin

// SX1262 LoRa SPI Pins
#define LORA_NSS        8
#define LORA_DIO1       14
#define LORA_NRST       12
#define LORA_BUSY       13

// GPS Hardware Serial Pins
#define GPS_RX_PIN      17
#define GPS_TX_PIN      18

// ----------------------------------------------------------------------------
// GLOBAL OBJECTS & CONSTANTS
// ----------------------------------------------------------------------------
SX1262 radio = new Module(LORA_NSS, LORA_DIO1, LORA_NRST, LORA_BUSY);
TinyGPSPlus gps;
HardwareSerial gpsSerial(1);

// State Management
enum SystemMode { MODE_NORMAL, MODE_SOS_BROADCAST, MODE_RESCUE_SEARCH };
SystemMode currentMode = MODE_NORMAL;

// Timers & Variables
unsigned long buttonPressStart = 0;
bool buttonHeld = false;
unsigned long lastTransmitTime = 0;
unsigned long lastBeepTime = 0;

// Prototype User Info
const char* USER_ID = "PETER";

// ----------------------------------------------------------------------------
// HELPER FUNCTIONS
// ----------------------------------------------------------------------------

// Triggers a brief pulse on the active piezo buzzer
void beep(int durationMs) {
  digitalWrite(PIN_BUZZER, HIGH);
  delay(durationMs);
  digitalWrite(PIN_BUZZER, LOW);
}

// PIEPS-style variable speed locator beep logic based on distance
void handlePiepsAudioFeedback(float distanceMeters) {
  unsigned long now = millis();
  int intervalMs = 2000; // Default slow beep (>500m)

  if (distanceMeters <= 500.0 && distanceMeters > 100.0) {
    intervalMs = 1000;  // Medium speed (100m - 500m)
  } else if (distanceMeters <= 100.0 && distanceMeters > 20.0) {
    intervalMs = 400;   // Fast speed (20m - 100m)
  } else if (distanceMeters <= 20.0) {
    intervalMs = 150;   // Rapid pulse (<20m)
  }

  if (now - lastBeepTime >= intervalMs) {
    lastBeepTime = now;
    beep(50);
  }
}

// Broadcasts an Emergency SOS LoRa packet with current GPS coordinates
void transmitSOSPacket() {
  String packet = "SOS:" + String(USER_ID) + ",";
  
  if (gps.location.isValid()) {
    packet += String(gps.location.lat(), 6) + "," + String(gps.location.lng(), 6);
  } else {
    packet += "NO_GPS_FIX,NO_GPS_FIX";
  }

  Serial.println("[LORA] Broadcasting Emergency SOS: " + packet);
  
  // Transmit packet over radio
  int state = radio.transmit(packet);
  if (state == RADIOLIB_ERR_NONE) {
    Serial.println("[LORA] SOS Packet Sent Successfully.");
  } else {
    Serial.print("[LORA] SOS Transmission Failed, Code: ");
    Serial.println(state);
  }
}

// ----------------------------------------------------------------------------
// MAIN SETUP
// ----------------------------------------------------------------------------
void setup() {
  Serial.begin(115200);
  
  // Hardware Pin Modes
  pinMode(PIN_SOS_BUTTON, INPUT_PULLUP);
  pinMode(PIN_BUZZER, OUTPUT);
  pinMode(PIN_VIBE_MOTOR, OUTPUT);

  digitalWrite(PIN_BUZZER, LOW);
  digitalWrite(PIN_VIBE_MOTOR, LOW);

  // Startup indication
  beep(100);
  
  Serial.println("=========================================");
  Serial.println(" The Walking Buddy - ESP32-S3 Firmware   ");
  Serial.println(" ⚠️ PROTOTYPE / UNTESTED BUILD ONLY       ");
  Serial.println("=========================================");

  // Initialize GPS Serial
  gpsSerial.begin(9600, SERIAL_8N1, GPS_RX_PIN, GPS_TX_PIN);

  // Initialize SX1262 LoRa Module (868.0 MHz EU / 915.0 MHz US)
  Serial.print("[LORA] Initializing SX1262... ");
  int state = radio.begin(868.0, 125.0, 9, 7, RADIOLIB_SX1262_SYNC_WORD_PRIVATE, 22);
  
  if (state == RADIOLIB_ERR_NONE) {
    Serial.println("SUCCESS!");
  } else {
    Serial.print("FAILED, code ");
    Serial.println(state);
  }
}

// ----------------------------------------------------------------------------
// MAIN LOOP
// ----------------------------------------------------------------------------
void loop() {
  unsigned long now = millis();

  // 1. Process Incoming GPS Stream
  while (gpsSerial.available() > 0) {
    gps.encode(gpsSerial.read());
  }

  // 2. Check Hardware SOS Button Hold (3-Second Safety Guard)
  if (digitalRead(PIN_SOS_BUTTON) == LOW) {
    if (!buttonHeld) {
      buttonHeld = true;
      buttonPressStart = now;
    } else if (now - buttonPressStart >= 3000 && currentMode != MODE_SOS_BROADCAST) {
      // 3 Seconds held -> Trigger SOS Mode
      currentMode = MODE_SOS_BROADCAST;
      Serial.println("[ALERT] 🚨 SOS MODE ACTIVATED BY USER! 🚨");
      
      // Triple pulse haptic confirmation
      for (int i = 0; i < 3; i++) {
        digitalWrite(PIN_VIBE_MOTOR, HIGH);
        delay(100);
        digitalWrite(PIN_VIBE_MOTOR, LOW);
        delay(100);
      }
    }
  } else {
    buttonHeld = false;
  }

  // 3. Mode State Machine
  switch (currentMode) {
    case MODE_NORMAL:
      // Standby mode: listens for incoming LoRa packets or GPS updates
      break;

    case MODE_SOS_BROADCAST:
      // Broadcast emergency location every 2 seconds
      if (now - lastTransmitTime >= 2000) {
        lastTransmitTime = now;
        transmitSOSPacket();
        beep(200); // Local siren beep
      }
      break;

    case MODE_RESCUE_SEARCH:
      // Rescuer mode: simulated proximity beep test (e.g. 85 meters away)
      handlePiepsAudioFeedback(85.0);
      break;
  }

  delay(10); // Yield to prevent WDT reset
}
