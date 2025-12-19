# Sorta-Flipper-Zero
At the cost of a flipper zero, I thought that I may never own one. Then I found a hackster.io page that went through a full DIY and cheap Flipper Zero and I thought I'd try it myself with some changes.

## Step 1 - Looking at the Flipper Zero Hardware
Before starting this project, I went through the Flipper Zero hardware documentation to understand how the device is built and what components it uses.

From this, I created the table below listing the main hardware blocks used in the Flipper Zero.
This table is used as a reference to understand the system, not something to copy directly.

### Flipper Zero – Hardware Component Overview

<img width="882" height="459" alt="Screenshot 2025-12-19 at 7 44 55 pm" src="https://github.com/user-attachments/assets/54e41be2-6ee9-4988-9df5-421abc2aec96" />

### Simplifying the Design (v1)
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

## Step 2 - v1 Hardware Selection (Module Based)
Based on the Flipper Zero reference design and the simplifications outlined above, the following components were chosen for the v1 module-based prototype.
This list reflects what will actually be used in the first working version of the project.

V1 - Selected Components:
<img width="732" height="303" alt="Screenshot 2025-12-19 at 8 00 04 pm" src="https://github.com/user-attachments/assets/b7dd60ea-bcbb-4b59-a580-b6a301e4e70c" />
