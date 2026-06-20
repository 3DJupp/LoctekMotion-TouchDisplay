# LoctekMotion-TouchDisplay

![ESP32-2432s028-lvgl_UI](https://github.com/user-attachments/assets/7297aa28-2764-4822-9046-17c50a5d4c75)
There is another version that is not using LVGL:
![LoctekMotion-TouchDisplay](https://github.com/user-attachments/assets/91f7d191-226c-42c4-b466-933921f3b59d)

> [!NOTE]
> ⚡ Eletronics are dangerous<br>
> 🤓 Tinkering with your smart desk might void your warranty. Proceed with caution. I tested it with one specific desk and one MCU series.

I got inspired by [Adrian's ESPHome touch display mount](https://github.com/akuehlewind/ESPHome-touch-display-mount) and [Mick's LoctekMotion IoT project](https://github.com/iMicknl/LoctekMotion_IoT), so thanks for this opportunity!  
Basically, I first found the [printable screen mount on Makerworld](https://makerworld.com/de/models/799188-desk-mount-for-touch-display-esp32-2432s028) and then started tinkering around my Flexispot-Desk. 

I got a **Flexispot E7Q**, also sold as **Odin/E7Q Pro** in the English-speaking market:  
- [Flexispot Odin (EN)](https://www.flexispot.com/flexispot-odin-4-leg-standing-desk)  
- [Flexispot E7Q (DE)](https://www.flexispot.de/elektrisch-hohenverstellbares-tischgestell-e7q.html)
- It is using a HS13B-1 controller, pinout similar to the HS01B-1

## Learnings  
- If the table/controller does not respond, try **unplugging the RJ45 connectors** to your display(s), whether it's an ESP32 or the original Loctek MCU which is probably STMicroelectronics-based.
- **Different versions of the "cheap yellow display"** – Mine has two USB inputs (USB-C and Micro-USB). The mounting holes were slightly off, so I used a 4/6mm drill to fix it (carefully).
- **Set limits for the desk** - Or the table will crash or interfere with obstacles.  
- **Disable the hardware UART** or at least the console on the ESP32.  
- Not all pins are needed for this project. At least the **🟣purple, ⚪white, 🟤brown** wires from the original cable are not required (**SWIM and Reset** are probably needed to program the controller in the factory).
- If you're unsure about the pinout, open the original controller and write down the cable colors and its function. I can also recommend [the archive page](https://github.com/iMicknl/LoctekMotion_IoT/blob/main/archive/esphome/README.md) on the LoctekMotion_IoT-Prject or a simple [google search](https://github.com/iMicknl/LoctekMotion_IoT/issues/12) to find the correct pins.
- The inbuilt display graphics from ESPHome are quite slow, so if you're having live values on the screen, then it is probably too slow. The new YAML for ESPHome features LVGL

## Required Parts
You might need some physical and virtual tools, such as: a screwdriver, pliers/something to trim the ethernet cable, 3d-printer, [ESPHome](https://esphome.io/) installed within [Home Assistant](https://www.home-assistant.io/) and some technical knowledge (not really programming, but you should be able to read those YAML files etc)
| Part | Count | Price (€) | Comment |
|------|------:|---------:|---------|
| **ESP32-2432S028** | 1 | ~17€ | "cheap yellow display" 2*USB: [ESP32-2432S028R (eBay)](https://www.ebay.de/itm/365089053020) |
| **Small screw** | 2 | ~0.10€ | ~3.5x16 (flathead), screws to connect the 3d-printed mount with the base |
| **RJ45-Push Fit-Keystone** | 1 | ~2.50€ | Easy RJ45 breakout-socket: [RJ45 Keystone Adapter (Amazon)](https://amzn.to/4h76LdO) |
| **RJ45-Patchcable 2m** | 1 | ~4€ | Connects controller and keystone module: [RJ45 Keystone Adapter (Amazon)](https://amzn.to/41kIgUN)) |
| **Surface Mount Keystone RJ45** | 1 | - | 3D-printable: [Makerworld Model](https://makerworld.com/de/models/768853-surface-mount-keystone-rj45-connector-parametric) |
| **Touch Display Mount** | 1 | - | 3D-printable: [Makerworld Model](https://makerworld.com/de/models/799188-desk-mount-for-touch-display-esp32-2432s028) |
| **Flexispot E7Q Desk Frame** | 1 | ~700€ | Other tables/controllers might be compatible [Flexispot E7Q (DE)](https://www.flexispot.de/elektrisch-hohenverstellbares-tischgestell-e7q.html) |

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

### Known Issue
- If you use USB while connected to the desk-controller, UART0 is blocked. So please disconnect USB and power the CYD just via the Desk or use another serial port (which is sharing its pins with the Led) . [See that issue on the repo ](https://github.com/3DJupp/LoctekMotion-TouchDisplay/issues/1#issuecomment-2797995654)

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

# Software
I decided to hop on that train and use ESPHome (quite new for me). I will provide two YAMLs which can easily installed from the Home Assistant GUI/ESPHome Addon.

## flexispot-e7q-esp32-2432s028-default_graphics
This one is basically a fork of [ESPHome-touch-display-mount](https://github.com/akuehlewind/ESPHome-touch-display-mount) but instead of using other HA components, i enabled it to control the LoctekMotion/Flexispot Desk.<br>
The UI is nice, but there are some considerations:
- In case the display is updated, that will take some time, so to get rid of that I decided to show no live values when manually lowering or rising the desk.
- 8 Bit color only, in the current (02/2025) ESPHome release there is a bug, so there's [a workaroud](https://github.com/akuehlewind/ESPHome-touch-display-mount/issues/4)
- Loads of YAML and lambdas, the UI is in German at that time, so if you're into translating it, you should probably adjust the weekdays. BTW: Höhe means height in German
- Only two presets are used: Preset 1 and 2, I did not use the "sit" and "stand" preset
- Buttons are deactivated in yaml, but no visual feedback for the user
- HA Time Sync replaced with NTP, but you can change that at any time
- Glyphs (for the symbols) and Letters adjusted, used anchors in the yaml

Here you will find [flexispot-e7q-esp32-2432s028-default_graphics.yaml](YAML/flexispot-e7q-esp32-2432s028-default_graphics.yaml)

## flexispot-e7q-esp32-2432s028-lvgl
This is the recommended YAML. It still borrows a lot from [ESPHome-touch-display-mount](https://github.com/akuehlewind/ESPHome-touch-display-mount) and the [LoctekMotion IoT project](https://github.com/iMicknl/LoctekMotion_IoT), but the whole UI (display, buttons, status icons) is now built with **LVGL**, which renders noticeably faster than the default graphics.
<br>What this version currently offers:
- **16-bit color** and a faster, smoother UI than `default_graphics` – including a live height value while the desk is moving.
- A **gradient bar** at the bottom that shows the current height. The min/max values come from the YAML, so the bar stays sane even when the desk overshoots its limits (these desks tend to over-/undershoot a little).
- **Button matrices** for presets and up/down with automatic touch mapping and far fewer lambdas than the old approach. The up/down buttons are automatically **disabled** once the desk reaches the configured min/max height.
- A **dead-man watchdog**: a touch-initiated hold move is force-stopped if no touch has been seen for ~400 ms, so the desk never keeps driving into an end stop with no finger on the screen.
- **Burn-in prevention** for the LCD: after the screensaver, an "antiburn" mode (moving snow) protects the panel, and the backlight dims and then turns off on idle.
- Quality-of-life details: a configurable **boot logo + spinner**, **status icons** (Home Assistant connection, serial activity), **NTP time/date** (German weekday & month names), and the **last used preset** is stored in flash and restored after a reboot. The last known height is also restored so the label is never empty on boot.
- The UI is less polished than the first version, but it is more flexible, faster, and gives live height feedback during movement.
- It is still **not a finished product**, so please take some time to validate that it works for your setup.

Here you will find [flexispot-e7q-esp32-2432s028-lvgl.yaml](YAML/flexispot-e7q-esp32-2432s028-lvgl.yaml)

## LVGL – Roadmap / ideas for future versions
The LVGL YAML works, but there is plenty of room to grow. These are notes-to-self / open ideas for anyone who wants to build on it – contributions welcome.

### UI / UX
- **On-device settings page**: adjust min/max height, brightness and language directly on the touch display, without going through Home Assistant.
- **Editable presets on the display**: e.g. long-press a preset button to store the current height, instead of relying on the control box's memory keys.
- **More informative screensaver**: show a large clock *and* the current desk height at a glance, instead of just the logo and the scrolling "tap to wake" text.
- **Clearer button states**: more obvious styling for disabled up/down buttons and for the active preset (a proper theme/style block instead of inline colors scattered across the YAML).
- **Meaningful height bar**: replace the rainbow gradient with zones that actually mean something (e.g. a sitting zone vs. a standing zone, or a marker at your favourite heights).
- **Reach/standstill feedback**: a short animation or color change when the desk arrives at a preset/target height.

### Use more of the CYD hardware
- **Ambient light sensor (LDR on GPIO34)**: drive the backlight brightness automatically instead of using fixed idle/active levels.
- **Buzzer / speaker**: optional audio feedback on button press or when a target height is reached.
- **RGB LED as a status indicator**: e.g. show "moving", "at preset" or "child lock active" via the on-board RGB LED (already wired up as a `light`).

### Performance & resources
- **Boot without the fixed 20 s delay**: the current `on_boot` blocks for a hard-coded 20 s before showing the main page. An event-driven boot (wait for time sync / the first height measurement) would feel snappier and more robust.
- **Font footprint**: the three Roboto sizes already share one glyph list – trimming sizes/bpp or dropping unused glyphs would free more flash.
- **Rotation & buffer tuning**: the UI is rotated in software (`rotation: 270`); evaluating hardware rotation and tuning `buffer_size` could reduce RAM/CPU load.

### Robustness & maintainability
- **Multi-language support**: button labels and the weekday/month names are currently hard-coded German. Moving the strings into substitutions would make translating trivial.
- **On-screen touch calibration**: the touch calibration is hard-coded for one unit. A guided on-screen routine would make the YAML portable across CYD batches, which are known to vary.
- **Theme/style definitions**: centralise colors, fonts and paddings into reusable LVGL styles instead of repeating them per widget.

### Integration
- **Richer on-screen status**: Wi-Fi signal strength icon, OTA update progress, or an error/notice toast when the control box stops responding.

