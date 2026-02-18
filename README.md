# 1HP-Smart-Water-Pump-with-Integrated-Wireless-Monitoring
An intelligent 1HP pump automation system with wireless tank-level monitoring and autonomous ON/OFF control based on configurable thresholds. Features dry-run, under/over-voltage, and power-failure protection. Comprises an indoor LCD-based controller with relay drive and a solar-powered ultrasonic sensor node transmitting data via 433 MHz RF.
# Key Features
# Main Controller Unit

![PUMP1](https://github.com/user-attachments/assets/091e2e3d-b19f-49e0-9509-1a47763a8a53)


16×2 LCD interface displaying real-time tank level (%), pump state, mains status, battery status, runtime timer, fault indicators, and system icons.

5-key navigation (Up/Down/Left/Right/OK) implemented via single analog input using resistor ladder technique.

Dual operating modes: Automatic (level-threshold based control) and Manual override mode.

Configurable pump cut-in threshold (10%, 20%, 30%, etc.).

Automatic pump cutoff at full tank or upon fault detection.

Dry-run protection through low-current sensing and intelligent shutdown.

Under-voltage protection for AC mains supply.

Configurable maximum runtime timer with auto shutoff.

Programmable periodic scheduling (6/8/12/24-hour intervals).

Power failure detection with battery-backed monitoring and level display during outage.

Comprehensive fault logging (power fail, under-voltage, dry run, sensor fault, low sensor battery, communication loss, timer trigger, etc.).

Audible buzzer and visual LED alarm indication.

User-accessible factory reset via system menu.
# Sensor Unit
Waterproof ultrasonic sensor (DYP-A02YYTW-V2.0) for accurate tank level measurement.
Solar panel + Li-ion battery based power source (no external power needed).
Very low power consumption — sleeps most of the time, wakes only to measure and transmit.
Dynamic sleep interval: faster sampling when tank is filling (to avoid overflow), slower when discharging (to save battery).
Transmits water level (%), battery status (good/low), charging status, and error flags.
One-time calibration for empty (low) and full (high) levels using button + LED feedback.
![pump2](https://github.com/user-attachments/assets/1af79237-cc8e-46be-b3d4-1b3a48f76191)

# System Operation
# Sensor Unit Operation

The sensor node operates in a dynamic sleep–wake cycle to optimize power consumption. At scheduled intervals, it wakes up, measures the distance to the water surface using an ultrasonic sensor, calculates the corresponding tank level percentage, verifies battery and solar charging status, and transmits 4-bit encoded data via 433 MHz RF using an HT12E encoder.

# Main Controller Operation

The controller receives RF data through an HT12D decoder. A valid transmission triggers an interrupt, and the 4 data lines (D0–D3) are sampled.

Status frames update system flags such as low battery, solar charging active, or sensor out-of-range.

Level frames are processed, stored, and converted to percentage for LCD display.

A 5-minute watchdog timer supervises RF communication. If no valid packet is received within this window, a transmitter error flag is asserted.

Simultaneously, the controller continuously monitors power board signals through optocoupler isolation:

Dry Run Detection: Evaluated only when the pump is running; confirmed after 30 s of continuous low-current condition.

Low AC Voltage: Always monitored; confirmed after 30 s when running, immediate when pump is OFF.

Mains Power Presence: Power failure confirmed with short validation (≈1 s during operation) and delayed recovery stabilization.

These conditions update internal flags such as dryRunFlag, lowVoltFlag, and pwrFailFlag.

# Pump Control Logic
# Automatic Mode

The pump turns ON when:

Tank level ≤ user-defined cut-in threshold

No active fault conditions

The pump turns OFF when:

Tank reaches full level

Runtime timer expires

Any fault condition occurs

# Manual Mode

User can force ON/OFF via buttons, but all safety protections (dry run, low voltage, timer expiry, tank full) remain active.

Scheduling

Supports periodic operation at fixed intervals (6, 8, 12, or 24 hours).

Auto-Off Timer

Acts as a final safety layer. The pump stops automatically after the configured duration even if no faults exist, and a timer alarm is generated.

# User Interface & Menu System
Home Screen

Displays large water level percentage along with status icons (power, pump state, timer active, auto mode, battery status, RF link status, alarms).

Navigation

Right: Enter main menu

Left: Open alarm menu (if active)

Up/Down: Scroll options

Enter: Select/confirm

Left/Right (edit mode): Modify values

Menu settings include:

Operation Mode

Cut-in Level

Pump Schedule

Low Voltage Cutoff

Auto-Off Timer

Dry Run Protection

LCD Brightness

Factory Reset

Settings are saved to EEPROM upon confirmation.

Menus automatically exit after 6 seconds of inactivity.

Alarm Handling

If any fault flag is active (dry run, low voltage, power fail, transmitter error, low sensor battery, out-of-range, timer alarm, etc.), the alarm flag is asserted.

Alarm LED blinks at 500 ms intervals.

Buzzer alerts during fault events.

Alarm menu displays only active faults for quick diagnosis.

If the pump is running, the buzzer emits a short tone every 30 seconds as an operational indicator.

Power Failure Behavior

During mains failure:

System switches to battery backup.

LCD backlight is dimmed to reduce power consumption.

Water level and essential status remain visible.

Pump resumes operation based on previous state and fault conditions after power restoration.

# Additional System Features

Automatic long-term system refresh (~10 days) for stability.

Startup sequence displays version info, performs LED/buzzer self-test, then enters home screen.

Factory reset restores default parameters and restarts the system after user confirmation.
Hardware Overview
Detailed hardware design (PCBs, power circuits, sensor wiring, enclosures) is in the /hardware/ folder.

Main Controller PCB → ATmega328P, LCD via PCF8574 I2C, RF receiver + HT12D, button ladder, relay driver.
Power PCB → VIPer22A SMPS (5V + 12V), battery charging (Li-ion TP4056 + lead-acid discrete), current sensing, dry run/low volt comparators.
Sensor Unit PCB → ATmega328P (8 MHz internal), ultrasonic (UART), RF transmitter + HT12E, solar + Li-ion charging, power switching MOSFET.

# Software Overview

The firmware source code is located in the /software/ directory and is organized by functional unit and version control.

Transmitter (Sensor Unit):
 featuring dynamic sleep management, UART-based ultrasonic interfacing, and integrated battery/solar status transmission.

Receiver (Main Controller):
Includes complete menu-driven user interface, LCD symbolic display framework, embedded safety logic, and EEPROM-based configuration management.

# Have questions or suggestions for improvement? Please feel free to open an issue.
