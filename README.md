# Deye Virtual Meter (ESPHome + go-e Controller)
This ESPHome project creates a "Virtual Meter" for Deye 3-Phase inverters.

It subscribes to live MQTT data published by a go-e Controller and emulates an Eastron SDM630 Modbus meter over RS485.

This allows the Deye inverter to perform rapid "Zero Export" throttling without requiring a physical SDM630 meter or CT clamps installed near the utility connection.

## Hardware Used
* **Microcontroller:** Waveshare ESP32-S3-RS485-CAN
* **Smart Meter:** go-e Controller (publishing telemetry via MQTT)
* **Inverter:** Deye 3-Phase Hybrid Inverter

## Requirements
* **The go-e Controller provides:**
* Grid Power
* Import Energy
* Export Energy
* L1/L2/L3 Voltage
* L1/L2/L3 Current
* L1/L2/L3 Power
* Frequency is not available from the go-e Controller MQTT topics. It may optionally be obtained from another source, such as a go-e Charger MQTT topic.
  
## Why this is needed
Deye inverters expect Modbus RTU communication over RS485 from an Eastron SDM630 meter (Slave ID 1). This ESP32 acts as a Modbus server, intercepting the Deye's requests and instantly replying with live voltage, current, power, frequency, import energy and export energy values obtained from MQTT topics published by the go-e Controller.
It is especially helpful if your inverter is mounted away from the grid feed line which does not allow you to use the clamp meters and if you do not have space to mount a Modbus RS485 meter (or if you do not want to pull another wire through your house..)

## Features
* **Correct Waveshare S3 Pins:** Pre-configured for the specific internal TX (17), RX (18), and Flow Control (21) pins of the Waveshare ESP32-S3 board.
* **MQTT-Based Data Source:** Subscribes to go-e Controller MQTT telemetry for grid power, voltages, currents, frequency, import energy and export energy values.
* **Lifetime Energy Counters:** Provides absolute import and export energy totals (SDM630 registers 72 & 74), preventing 0x48 exception errors and satisfying Deye's requirement for cumulative energy values.

## go-e Controller mapping
* **CCP:**
*0 = Home Power
*  1 = Grid Power
* 2 = Car Power
* 4 = Solar Power
* **CEC:**
* [1][0] = Grid Import Energy
* [1][1] = Grid Export Energy
* **USV:**
* u1 = L1 Voltage
* u2 = L2 Voltage
* u3 = L3 Voltage
* **ISV:**
* [0] = L1 Measurements
* [1] = L2 Measurements
* [2] = L3 Measurements

## Installation
1. Things are quite a bit easier, if you have go-e Controller already connected to a HomeAssistant and receiving MQTT.
2. Connect your ESP32 with a USB-C to your computer and install ESPHome Device Builder.
3. Use ESP32-S3 DevKitC-1 as board.
4. Connect to Wifi
5. Create the MQTT connection, IP and username and password needed.
6. The ESPHome can now also be found in HA as a new device, add the device.
7. Note that you need to change the XXXXXX in the YAML to correspond to your go-e Controller serial number.
8. Flash the code to your ESP32 via ESPHome.
9. Wire the RS485 A+ and B- terminals on the Waveshare board to the `Meter-485` RJ45 port on the Deye inverter (Usually Pin 1 / Pin 2, or Pin 7 / Pin 8 depending on the firmware).
10. Set the Deye inverter to `Eastron` in the Advanced Meter settings. First `Read`, then set values, then `Setup`.
11. After the steps were followed the Waveshare ESP32-S3-RS485-CAN should be flashing Blue and Green frantically which means the Modbus data is requested and provided  successfully.
   
<img width="217" height="350" alt="image" src="https://github.com/user-attachments/assets/b3c2b27b-182f-463a-b204-1a053a1c1edf" /> <img width="250" height="392" alt="image" src="https://github.com/user-attachments/assets/99199dc5-e4da-4ad4-b6d4-a5a465dc2bac" />

<img width="300" height="625" alt="image" src="https://github.com/user-attachments/assets/f7bddade-b12e-426a-bc66-e1f55c3af69d" />
