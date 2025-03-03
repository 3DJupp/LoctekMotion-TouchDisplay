# LoctekMotion-TouchDisplay  

I got inspired by [Adrian's ESPHome touch display mount](https://github.com/akuehlewind/ESPHome-touch-display-mount) and [Mick's LoctekMotion IoT project](https://github.com/iMicknl/LoctekMotion_IoT), so thanks for this opportunity!  
Basically, I first found the [printable screen mount on Makerworld](https://makerworld.com/de/models/799188-desk-mount-for-touch-display-esp32-2432s028) and then started tinkering around my Flexispot-Desk. 

I got a **Flexispot E7Q**, also sold as **Odin/E7Q Pro** in the English-speaking market:  
- [Flexispot Odin (EN)](https://www.flexispot.com/flexispot-odin-4-leg-standing-desk)  
- [Flexispot E7Q (DE)](https://www.flexispot.de/elektrisch-hohenverstellbares-tischgestell-e7q.html)
- It is using a HS13B-1 controller, its similar to the HS01BB-1

## Learnings  

- If the table/controller does not respond, try **unplugging the RJ45 connectors** to your display(s), whether it's an ESP32 or the original Loctek MCU which is probably STMicroelectronics-based.
- There are many different versions of the **"cheap yellow display"** – I came across one with **two USB inputs** (USB-C and Micro-USB). The mounting holes were slightly off, so I used a **4mm drill** to fix it.  
- **Set limits for the desk**, otherwise, it might interfere with obstacles.  
- **Disable the hardware UART** or at least the console on the ESP32.  
- A **RJ45-Push Fit-Keystone** is the easiest solution to connect to an RJ45 port. I used this one from Digitus: [RJ45 Keystone Adapter (Amazon)](https://amzn.to/4h76LdO).  
- I also **mounted the keystone to the table** using this model: [Surface Mount Keystone RJ45 (Makerworld)](https://makerworld.com/de/models/768853-surface-mount-keystone-rj45-connector-parametric).  
- Not all pins are needed for this project. At least the **purple, white, and brown wires** from the original cable are required (**SWIM and Reset** are probably needed to program the controller in the factory).
- If you're unsure about the pinout, open the original controller and write down the cable colors and its function. I can also recommend [the archive page](https://github.com/iMicknl/LoctekMotion_IoT/blob/main/archive/esphome/README.md) on the LoctekMotion_IoT-Prject or a simple [google search](https://github.com/iMicknl/LoctekMotion_IoT/issues/12) to find the correct pins.

# Pinout and assignment
The following picture and the tables showing the pin usages/assignments, cable colors for the **RJ45-wiring**

## RJ45 Pinout Table / HS13B-1
This table shows the RJ45 pin assignments with the corresponding wire colors for **LoctekMotion HS13B-1** and **Ethernet T568B** wiring standards. 

| RJ45 Pin | Name   | HS13B-1 Color | Ethernet / T568B Color |
|----------|--------|--------------|-----------------------|
| 8        | **+5V / VDD** | 🟡 Yellow  | 🟤 Brown            |
| 7        | **GND**    | 🔵 Blue      | ⚪🟤 White-Brown     |
| 6        | **TX**     | ⚫ Black     | 🟢 Green             |
| 5        | **RX**     | 🟢 Green     | ⚪🔵 White-Blue      |
| 4        | **PIN 20** | 🔴 Red       | 🔵 Blue             |
| 3        | N/A    | 🟣 Purple    | ⚪🟢 White-Green     |
| 2        | SWIM   | ⚪ White     | 🟠 Orange           |
| 1        | RESET  | 🟤 Brown     | ⚪🟠 White-Orange    |

**Example:** Yellow cable/Pin 8 is used for +5V voltage on the Loctek RJ45, it will be Brown on a standard ethernet cable. 🟡->🟤

## ESP32-2432S028 Pinout
Here is an overview of the ESP-Pinout. Unfortunately, some Pins have multiple functions, preconfigured pullups, so i used the hardware UART and IO22. The Pin headers are 2/4 pin JST headers, 1.25mm pitch
<img align="left" width="100%" src="https://github.com/user-attachments/assets/d65934dd-1dfc-4901-b5ab-876e6a5c5270">
<br>Picture source: [reddit.com](https://www.reddit.com/r/esp32/comments/1govf1k/esp32_2432s028_help_please)

## P5/Serial port connection
The following table shows the pin assignments for the **P5** connector. **All four pins are required**, used for power and data.

| Pin | Function  | Color (JST) | Color (T568B) |
|-----|----------|-------------|---------------|
| 1   | **Vin (5V)**  | 🔴 Red      | 🟤 Brown      |
| 2   | **TXD**       | ⚫ Black    | 🟢 Green      |
| 3   | **RXD**       | 🟡 Yellow   | ⚪🔵 White-Blue |
| 4   | **GND**       | 🟢 Green    | ⚪🟤 White-Brown |

## CN1/IO2 Pinout
The following table shows the pin assignments for the **CN1** connector. Only **IO22** is required, GND is optional, already satisfied by the first connector.

| Pin | Function     | Color (JST) | Color (T568B) |
|-----|------------|-------------|---------------|
| 1   | 3V3        | ⚫ Black    |               |
| 2   | **IO22 / PIN20** | 🔵 Blue     | 🔵 Blue       |
| 3   | IO27 (Pullup 10k 3V3) | 🟡 Yellow   |               |
| 4   | GND        | 🔴 Red      | ⚪🟤 White-Brown |

**Legend:**  
- ⚪🟠 = White in combination with the second color of the twisted pair e.g. ⚪🟠 = White-Orange, 🟠 = Orange
- Ethernet colors are based on the **T568B** standard, if using **T568A**, the connectors use other colors
- **Bold** means this pin or function is mandatory
- JST (P5/CN1) wire colors might differ, please check it carefully.
