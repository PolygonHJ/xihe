# Xihe
**Xihe** is a self-sustaining, ultra-low-power sensing platform designed for long-lifetime deployments with zero maintenance. Powered by ambient energy sources such as solar or background RF, the system stores energy in supercapacitors until it reaches a threshold sufficient to activate, sense, and transmit data wirelessly. Once depleted, it enters a dormant state and repeats the cycle.

The system is designed to be as small and unobtrusive as possible, enabling it to be placed in an environment, monitor passively, and be forgotten—both visually and in terms of maintenance.


## System Overview
The platform consists of:

### 1. **Main Board — "Xihe-Core"**
- Hosts the energy harvesting and power management circuitry,
- Includes high-capacitance supercapacitor storage,
- Integrates a **Seeed XIAO nRF54L15** module for BLE/Zigbee communication,
- Provides a modular connector interface for sensor boards (I²C, SPI, UART, Analogue),
- Manages energy thresholds and system wakeup logic.

### 2. **Sensor Modules**
- Plug-in sensor boards for flexibility,
- Operate only during active energy windows to minimize load,
- Interchangeable to allow flexibility in deployment.

### 3. **Energy Storage Modules**
- Optional add-ons for extended runtime or energy reserve (Supercapacitors or Batteries),
- Connect to the side or bottom of the board for flexibility,
- Extend active life at the cost of increased physical size.

## Features
- ⚡ **Ambient Energy Harvesting**: Solar (single-cell) supported; RF harvesting planned,
- 🔋 **Supercapacitor-Based Power Storage**: Maintenance-free,
- 📡 **Low-Power Wireless Comms**: BLE/Zigbee-ready for periodic broadcast,
- 🧩 **Modular Sensing**: Swappable sensor boards and energy modules for application-specific deployments,
- 🔄 **Cyclic Operation**: Charge → Activate → Sense → Transmit → Sleep,
- ♻️ **Designed for Longevity**: No batteries, no wires, no maintenance

## Use Cases
The project is intended for low-effort, low-maintenance monitoring in home automation or other low-frequency sensing environments.

Example sensing applications:
- Light  
- Temperature  
- Humidity  
- Moisture  
- Open/Close (e.g. reed switch)  

## Status
The hardware is under active development. No hardware for software has been produced or tested yet.

# Future Ideas
- Energy storage modules,
- RF energy harvesting,
- Modularise harvesting and energy storage sections.
