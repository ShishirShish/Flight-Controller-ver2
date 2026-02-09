🛩️ SEC Flight Controller (Dual-Computer Architecture)

This repository contains the hardware design of a second-generation (SEC) Flight Controller, featuring a dual-microcontroller architecture with clear separation between real-time flight control and navigation / sensor processing.
The design emphasizes determinism, fault isolation, clean power domains, and scalable peripherals, inspired by aerospace and safety-critical embedded systems.

🧠 System Architecture Overview
Battery / Power
      |
      v
+------------------+
| Power Management |
+------------------+
        |
        +-------------------+
        |                   |
        v                   v
+----------------+   +----------------+
| Flight Control |   | Navigation     |
| Computer (FCC) |<->| Computer (NAVC)|
+----------------+   +----------------+
        |                   |
        v                   v
   Actuators / PWM       Sensors / GPS

🔹 Major Subsystems

1️⃣ Power Subsystem
Battery input up to 26 V
Precision current sense resistor (10 mΩ)
INA219 voltage / current / power monitoring (I²C)
High-current buck regulator (5 V rail)
Dedicated 3.3 V LDO for digital and sensor domains
SGND / PGND separation for noise control
Power-good, enable, and status monitoring

2️⃣ Flight Control Computer (FCC)

Role:
Real-time flight control, actuator outputs, and deterministic tasks.

MCU:
STM32F2 / STM32F4-class MCU
Key Responsibilities
PWM generation for ESCs / servos
Hard real-time control loops
Command interface to NAVC
External SPI flash for logging/config
EEPROM for configuration storage

Key Interfaces
UART (FCC ↔ NAVC)
I²C (shared / isolated buses)
SPI (external flash)
USB (programming + debug)
SWD debug header

3️⃣ Navigation Computer (NAVC)

Role:
Sensor fusion, navigation, and high-level state estimation.

MCU:
STM32F405RG-class MCU
Connected Sensors
IMU (Accelerometer + Gyroscope)
Magnetometer
Absolute pressure sensor (barometer)
Temperature sensor
GNSS (u-blox MAX-8Q)

Key Features
Dedicated sensor I²C bus
Interrupt-driven IMU & MAG
GNSS backup battery support
Independent boot and reset control
Debug UART via USB-to-UART bridge

🔁 FCC ↔ NAVC Communication
UART (bi-directional command & telemetry)
I²C (optional shared peripherals)
GPIO handshake lines
Series resistors for signal integrity and isolation

This separation allows:
Deterministic control on FCC
Computationally heavier navigation on NAVC
Easier fault containment and debugging

🔌 Peripherals & Interfaces
FCC Peripherals
PCA9685 PWM driver (16-channel)
External SPI flash memory
I²C EEPROM
Multiple UART expansion headers
ADC inputs
Servo / ESC connectors (5 V tolerant)
NAVC Peripherals
IMU + Magnetometer
Barometer + Temperature sensor
GNSS module with SMA antenna
EEPROM
Debug and telemetry UARTs

🧪 Clocking & Reset Strategy
High-speed external crystals (25 MHz) for both MCUs
Separate reset switches for FCC and NAVC
Boot-mode selection via hardware switches
Internal NRST pull-ups used where applicable

🛠️ Programming & Debug
USB-FS programming support
Dedicated SWD headers for FCC and NAVC
USB-to-UART bridges for runtime debug
Jumper-selectable USB VBUS routing

📐 Design Considerations
Clear power-domain separation
Extensive local decoupling
Ferrite bead filtering for VDDA
Series resistors on inter-MCU signals
EMI-aware sensor and GNSS routing
Designed with flight safety and expandability in mind

🧰 Tools Used
KiCad EDA 9.0.6
STM32 Reference Manuals & AN2606
Sensor manufacturer datasheets
Aerospace-style schematic partitioning

🚀 Intended Applications
Advanced UAV / drone platforms
Research flight controllers
Navigation & sensor-fusion experimentation
Safety-critical embedded system studies
FCC / NAVC split-architecture learning
