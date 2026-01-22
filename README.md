![Flipper Image](https://github.com/user-attachments/assets/ed01836c-34e8-4d0d-b266-6dd59ebea9cc)

# Sorta-Flipper-Zero by aaakum4 (Jake)
At the cost of a flipper zero, I thought that I may never own one. Then I found a hackster.io page that went through a full DIY and cheap Flipper Zero and I thought I'd try it myself with some changes. With this project, I should also learn a ton about PCB's and how electronics work.

V1 is a modular based project to ensure that it will work as planned, then, , I will completely custom make it for V2. 

Features: 
- Bluetooth Low Energy (BLE)
- Sub-GHz RF support (433 MHz)
- NFC / RFID functionality
- Infrared (IR) transmit and receive
- On-device display
- Physical navigation and action buttons
- Audible feedback via buzzer
- MicroSD card storage
- USB-C connectivity
- External antenna support

Not included (compared to a standard Flipper): 
- iButton / 1-Wire support - Capacitive touch input
- Internal battery charging and management
- Integrated antennas
- Fully enclosed, consumer-ready housing (I will make one for v2)


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


## Step 5 - Sorting and routing the board

I am now a new man...

This was my third time routing a PCB, and it was a mix of improvement… and pain. The Xiao MCU I used for the Macropad and the ESP32-S3-WROOM-1u for the development board don’t come close to the challenge I just faced. The STM32WB5MMG is tiny but packed with pins, making it a real test of patience. That said, I’m finally done, and I couldn’t be happier.

There were multiple reroutes along the way, mostly caused by pin congestion around the MCU and the sheer number of external connectors. Some compromises were necessary, like tighter clearances and smaller vias, but in the end, the board reached a fully routable and manufacturable state.

While it’s not perfect, this routing pass made me really proud of how far I’ve come since the Macropad project. It was a clear reminder that schematics are the easy part—routing is where designs are truly made or broken.

For this project, I worked with a 4-layer board (my first board over 2 layers) to manage density and RF performance, especially around the RF/Bluetooth section. I created a dedicated antenna keep-out zone to avoid copper interference, routed USB D+ and D− as a matched, parallel pair to maintain signal integrity, and widened power traces to 0.5 mm to reduce voltage drop.

Given the tight board size, I had to selectively use smaller vias and reduced trace widths in congested areas to complete the routing without violating design rules. To make assembly easier, I also added silkscreen boxes around each functional module, improving clarity and helping with placement. Looking back, having dedicated GND and +3.3 V layers definitely made this step much more manageable compared to what it would’ve been without them.

### The board
Size: 125mm x 70mm (quite alot bulkier than a normal flipper, I have no clue how they do it)
Vias:
1) Defult: 0.6 x 0.3
2) Smaller (Due to cramped space): 0.45 x 0.2
Routes:
1) Defult: Clearance = 0.2, Width = 0.2
2) Power: Clearance = 0.2, Width = 0.5
3) Smaller: Clearance = 0.2, Width = 0.15

<p align="center">
<img width="2598" height="1466" alt="image" src="https://github.com/user-attachments/assets/035ce808-6c41-4c7e-9e05-288f42e071db" />
</p>


#### Layer 1:
<p align="center">
<img width="2600" height="1466" alt="image" src="https://github.com/user-attachments/assets/a24988b5-cf17-4949-b92b-a8bd9ab0d61d" />
</p>


#### Layer 2: GND
<p align="center">
<img width="2596" height="1462" alt="image" src="https://github.com/user-attachments/assets/e4dba347-f015-4580-ac79-21e326f8adf7" />
</p>


#### Layer 3: +3V3
<p align="center">
<img width="2598" height="1466" alt="image" src="https://github.com/user-attachments/assets/4359205f-a80f-4790-9d6d-b17142a36dbe" />
</p>


#### Layer 4:
<p align="center">
<img width="2600" height="1468" alt="image" src="https://github.com/user-attachments/assets/5b34b2a4-9172-4abb-9c7e-0693838be588" />
</p>


#### 3D:
<p align="center">
<img width="1790" height="1012" alt="image" src="https://github.com/user-attachments/assets/2b3c9ca5-742f-4cdd-9cc4-b688d8d68372" />
</p>

<p align="center">
<img width="1764" height="980" alt="image" src="https://github.com/user-attachments/assets/3ac5b464-c784-4f7a-a1a0-a05d8c660e8c" />
</p>

Estimated Time:
~2 hours of planning & component placement
~6 to 8 hours of routing + rerouting
~2 to 3 hours of cleanup, checks, and second-guessing everything

(Plus bonus time staring at the screen being stupid)

## Step ??? - Spontaneous Micro Project

For v2 of the Sorta Flipper Zero, I’ll most likely need to make a copper trace antenna for the NFC. For V1, I’m using a module, but I didn’t want to jump into V2 blindly. I decided to spend some time understanding how the module works, what I can bridge across, and even built my own small NFC card as an introduction to what I’ll need to do in V2.

Here’s the datasheet for the NFC module I hope to use: ST X-NUCLEO-NFC08A1

One thing I quickly noticed is that the antenna matters a lot. The orientation, size, and shape of the coil strongly affect the read range, and even small changes in trace layout or nearby copper can weaken the signal. This made it clear that designing a custom antenna will require careful attention.

I also learned that matching the antenna to the module is critical. The module expects the antenna to have roughly the right inductance; otherwise, communication becomes unreliable. Experimenting with the card showed me how adding or removing a turn in the coil noticeably changes performance.

Using the module is convenient because it handles much of the tuning internally. But seeing it in action made it obvious that if I want a custom copper trace for V2, I’ll need to account for these details myself.

This is the PCB I made from a simple schematic. I originally included an LED, but removed it in the PCB for the sake of readability and clarity. The file is in its own folder.

#### PCB:
<p align="center">
<img width="1038" height="1374" alt="image" src="https://github.com/user-attachments/assets/b18a631c-3055-4ea1-85f8-47ff28d642e9" />
</p>


#### 3D:
<p align="center">
<img width="816" height="1092" alt="image" src="https://github.com/user-attachments/assets/39d9e2fb-2350-4c02-84f4-f4e8903ca3ee" />
</p>

<p align="center">
<img width="840" height="1094" alt="image" src="https://github.com/user-attachments/assets/09f3df60-b258-4401-aab7-110c7a275a12" />
</p>

This step is not nesassary at all, but I truly found it really helpful with general understanding of this concept and I think it will be even more helpful when I make v2.

## Parts
Since alot of this iteration will be using modules there isn't that much soldering. Here is the parts list from JLCPCB PCBA.

<p align="center">
<img width="1868" height="1678" alt="image" src="https://github.com/user-attachments/assets/0f2aeabc-48e1-4267-80d2-962dc84588ab" />
</p>


## License

This project is licensed under the GNU General Public License v3.0 (GPL-3.0).

This project includes and is derived from code from:
- flipperzero-firmware by skotopes (Top contributor)
  Licensed under GPL-3.0

