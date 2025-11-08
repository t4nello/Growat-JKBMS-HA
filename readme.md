# Growatt + JK-BMS ESPHome configurations

This repository contains ready-to-use ESPHome configurations to monitor and integrate a JK-BMS and a Growatt inverter.

- `jk-bms.yaml` — ESPHome configuration that connects to a JK-BMS via BLE using the community component.
- `growatt.yaml` — ESPHome configuration reading Growatt inverter data over Modbus UART.
- `combined.yml` — a combined configuration that includes both BLE (JK-BMS) and Modbus (Growatt) in a single firmware.

Note: The JK-BMS integration/component used in these configurations is taken from: https://github.com/syssi/esphome-jk-bms

These files expose many entities (sensors, binary_sensors, text_sensors, switches, etc.) suitable for Home Assistant using ESPHome.

## Quick facts

- Platform: ESP32 (`esp32dev` in the YAMLs).
- ESPHome: minimum version is specified in the files (e.g. 2024.6.0) — check the top of each YAML.
- External component for JK-BMS: `syssi/esphome-jk-bms` (referenced in `jk-bms.yaml` and `combined.yml`).
- Growatt communication: Modbus over UART (default TX: GPIO16, RX: GPIO17, 9600 baud in these examples).

## Before flashing — things to update

1. Wi-Fi credentials
   - Some files use `!secret wifi_ssid` / `!secret wifi_password`. Add these to your `secrets.yaml` or replace them (not recommended to hard-code credentials).

2. BMS MAC address and protocol version
   - Check the substitution entries in `jk-bms.yaml` and `combined.yml`:
     - `bms0_mac_address` — set the MAC address of your JK-BMS BLE module.
     - `bms0_protocol_version` — e.g. `JK02_32S` or another version matching your BMS.

3. Modbus / UART settings
   - Verify the UART pins (TX/RX) and baud rate match your physical connection to the Growatt inverter.

4. External components
   - The JK-BMS integration is referenced via `external_components: github://syssi/esphome-jk-bms@main`. You can pin a tag or change the source if required.

## What each file contains (summary)

- `jk-bms.yaml`:
  - BLE client for the JK-BMS (via the syssi component).
  - Sensors for cell voltages, cell resistances, currents, temperatures, SOC, energy, etc.
  - Status LED, buttons, and energy counters.

- `growatt.yaml`:
  - Modbus controller reading registers from the Growatt inverter.
  - Sensors for AC power, voltage, current, frequency, temperatures, PV energies, battery state, fan speeds, etc.
  - LED logic indicating connection state (WiFi / API).

- `combined.yml`:
  - Combines both files into a single ESP firmware that handles both the BMS (BLE) and the Growatt Modbus interface.

## Lovelace cards:
Custom lovelace cards compatible with my yamls based on https://github.com/syssi/esphome-jk-bms/discussions/230
[Outcome of the lovelace](lovelace.png)

## Notes and best practices

- Back up your current configurations before making changes.
- Test on a single device first (e.g., flash `jk-bms.yaml` alone, then `growatt.yaml`).
- OTA updates temporarily suspend BLE connections in these examples (`ota.on_begin` has logic to turn off BLE client switch).
- For troubleshooting, increase logging (`logger.level: DEBUG`) and check ESPHome logs.
