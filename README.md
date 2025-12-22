![Flipper Image](https://github.com/user-attachments/assets/ed01836c-34e8-4d0d-b266-6dd59ebea9cc)

# Sorta-Flipper-Zero
At the cost of a flipper zero, I thought that I may never own one. Then I found a hackster.io page that went through a full DIY and cheap Flipper Zero and I thought I'd try it myself with some changes. With this project, I should also learn a ton about PCB's and how electronics work. The first iteration of this project will be modular based, then, once I've confirmed that it would in theory work, I will completely custom make it.

## Step 1 - Looking at the Flipper Zero Hardware
Before starting this project, I went through the Flipper Zero hardware documentation to understand how the device is built and what components it uses.

https://docs.flipper.net/zero/development/hardware/tech-specs
https://docs.flipper.net/zero/development/hardware/schematic

<p align="center">
<img width="1248" height="680" alt="image" src="https://github.com/user-attachments/assets/dcb4107f-29a3-432d-8904-7ec437f3a7b1" />
</p>

From this, I created the table below listing the main hardware blocks used in the Flipper Zero.
This table is used as a reference to understand the system, not something to copy directly.

### Flipper Zero – Hardware Component Overview

<p align="center">
<img width="882" height="459" alt="Screenshot 2025-12-19 at 7 44 55 pm" src="https://github.com/user-attachments/assets/54e41be2-6ee9-4988-9df5-421abc2aec96" />
</p>

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

<p align="center">
<img width="732" height="303" alt="Screenshot 2025-12-19 at 8 00 04 pm" src="https://github.com/user-attachments/assets/b7dd60ea-bcbb-4b59-a580-b6a301e4e70c" />
</p>

## Step 3 – Schematic Design
With the overall architecture defined, I moved on to creating the full schematic in KiCad. The goal at this stage was to produce a complete, electrically correct design that could realistically be turned into a PCB, while still keeping the project modular and easy to iterate on.

I started by placing the STM32WB5MMGH6TR module and wiring all required power pins according to ST’s reference designs. This included separating the digital and analog supplies, tying VREF+ to VDDA, and correctly handling VBAT and VDDUSB. Decoupling capacitors were added close to each supply pin to ensure stable operation.

Next, the power system was designed around USB-C input. A main 3.3 V rail powers the MCU and core logic, while a second 3.3 V rail is used for external peripherals. 

Peripheral interfaces were then added. Instead of hard-coding specific modules, generic headers were used for NFC, Sub-1 GHz RF, display, IR, vibration, and expansion. These headers are labeled to match common module pinouts, allowing different breakout boards to be tested without changing the core design. Shared buses like SPI were carefully routed with separate chip-select signals.

Finally, the schematic was cleaned up and checked for electrical correctness. ERC warnings were addressed, unused pins were left intentionally for future use, and the design was reviewed to ensure it matched the original goals of being practical, modular, and suitable for a first hardware revision.

<p align="center">
<img width="1569" height="901" alt="Screenshot 2025-12-20 at 10 05 20 pm" src="https://github.com/user-attachments/assets/723d1095-1428-4652-acc0-0ada018f26e9" />
</p>

This step, to be honest, was kinda cooked. I went in a bit over my head with this and it took wayyyy longer than I had hoped and probably needed.

## Srep 4 - Schematic fixes and cleanup

After the initial schematic was complete, I spent time going back through it to fix a number of electrical and structural issues flagged by ERC. This step was mainly about correcting mistakes rather than adding new features.

Most of the changes were around power domains, making sure VDD, VDDA, VREF+, VBAT, and VDDUSB were connected correctly and matched the STM32WB module datasheet. I cleaned up grounding, added and adjusted local decoupling capacitors, and removed a few incorrect connections that were previously masking ERC errors instead of solving them properly.

I also fixed the Bluetooth RF path, reworking the RF_OUT pin so it is routed through a DC-blocking capacitor to a u.FL connector. This replaces earlier incorrect handling of the RF pin and makes the onboard BLE functionality usable while keeping the design clean and standards-compliant.

<p align="center">
<img width="1410" height="809" alt="Screenshot 2025-12-22 at 11 09 07 pm" src="https://github.com/user-attachments/assets/d68cebd9-bc6c-4050-b0d2-89e7f463cfd1" />
</p>

<p align="center">
<img width="666" height="415" alt="Screenshot 2025-12-22 at 11 08 50 pm" src="https://github.com/user-attachments/assets/2785451a-f76c-4d7c-ae50-5598b944b082" />
</p>

This is an amazing sight, no ERC errors 🙏🏻
