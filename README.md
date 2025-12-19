# Sorta-Flipper-Zero
At the cost of a flipper zero, I thought that I may never own one. Then I found a hackster.io page that went through a full DIY and cheap Flipper Zero and I thought I'd try it myself with some changes.

## Step 1 – Looking at the Flipper Zero Hardware
Before starting this project, I went through the Flipper Zero hardware documentation to understand how the device is built and what components it uses.

From this, I created the table below listing the main hardware blocks used in the Flipper Zero.
This table is used as a reference to understand the system, not something to copy directly.

### Flipper Zero – Hardware Component Overview

<img width="414" height="725" alt="Screenshot 2025-12-19 at 7 38 44 pm" src="https://github.com/user-attachments/assets/50442ecb-92a9-4e22-b3bf-adf8efb579f3" />
Subsystem	Component / IC	Purpose
MCU	STM32WB55RG	Main processor + BLE
BLE RF	Matching network + antenna	Bluetooth communication
Display	ST7567 (128×64 LCD)	User interface
Sub-1 GHz RF	CC1101	Sub-1 GHz RX/TX
Sub-1 GHz Antenna MUX	BGS13S4N9	Antenna band selection
NFC (13.56 MHz)	ST25R3916	NFC reader
NFC Antenna	PCB antenna	NFC coupling
LF RFID (125 kHz)	Discrete analog front-end	Low-frequency RFID
Infrared TX	IR LED + driver	IR transmission
Infrared RX	TSOP75338	IR reception
Storage	MicroSD card slot	External storage
Power	Li-Po battery	Portable power
Battery Charging	BQ25896	Battery charger
Battery Gauge	BQ27220	Battery monitoring
RGB LED Driver	LP5562	Status LEDs
Buzzer	Passive buzzer	Audio feedback
Buttons	Tactile switches	User input
Reset Logic	Logic gates + capacitors	Button-combo reset<img width="586" height="305" alt="image" src="https://github.com/user-attachments/assets/a6ab0eeb-f2a1-43e6-aad4-e063b668a653" />

### Simplifying the Design (V1)
To keep the first version realistic and achievable, a few things were intentionally simplified.

#### USB Power Only
The device is powered only through USB, with no internal rechargeable battery.
This removes a lot of complexity around battery charging and safety, and makes early testing much easier. Portability isn’t the focus for the first version.

#### BLE Using a Module or Dev Board
Instead of designing a full BLE RF circuit, this project uses either an STM32WB5MMG module or a NUCLEO-WB55RG during early development.
This avoids antenna tuning issues and lets me focus more on firmware and overall system behaviour.

#### Simpler Sub-1 GHz Antenna
The Flipper Zero uses an antenna multiplexer to optimise different Sub-1 GHz bands. For this project, that part is left out.
A single antenna is used instead, which means reduced range, but much simpler hardware and easier bring-up.

#### Physical Reset Button
Rather than copying the Flipper Zero’s reset button combination logic, this design uses a dedicated reset button.
This keeps the schematic simpler and is more practical while debugging.
