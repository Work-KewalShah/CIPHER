# CIPHER (Ongoing Project)

<div align="center">
  <img width="700" alt="CIPHER Full Setup" src="https://github.com/user-attachments/assets/161a707d-4dd9-4985-9779-caeaf672e161" />
</div>

<br/>

<div align="center">
  A Raspberry Pi 5-based handheld cyberdeck - retro gaming console on the surface, portable pentesting device underneath.
</div>

---

## Badges

<div align="center">

![Platform](https://img.shields.io/badge/Platform-Raspberry%20Pi%205-c51a4a)
![OS](https://img.shields.io/badge/OS-Batocera%20v42-blue)
![Controller](https://img.shields.io/badge/Controller-ESP32--S3-teal)
![Language](https://img.shields.io/badge/Code-Python%20%7C%20C%2B%2B-orange)
![Status](https://img.shields.io/badge/Status-Active%20Build-yellow)
![License](https://img.shields.io/badge/License-MIT-green)

</div>

---

## Gallery

<div align="center">

| | |
|:---:|:---:|
| <img width="340" alt="All components connected view 1" src="https://github.com/user-attachments/assets/7c87cd23-4ed6-4850-a60b-e4ca183fbd2f" /> | <img width="340" alt="Full setup with display on monitor visible" src="https://github.com/user-attachments/assets/161a707d-4dd9-4985-9779-caeaf672e161" /> |
| Full setup - all modules connected | Display and streaming active |
| <img width="340" alt="Complete final setup till now layed on table" src="https://github.com/user-attachments/assets/63e7f747-1c66-40bd-90ba-82397d338792" /> | <img width="340" alt="Game streaming test on Pi via Moonlight on Laptop" src="https://github.com/user-attachments/assets/0f81ff4b-6edd-4fa0-8fdc-86789b5519c5" /> |
| All modules laid out | PC game streaming via Moonlight |

</div>

---

## Table of Contents

- [CIPHER (Ongoing Project)](#cipher-ongoing-project)
  - [Badges](#badges)
  - [Gallery](#gallery)
  - [Table of Contents](#table-of-contents)
  - [What Is This](#what-is-this)
  - [System Architecture](#system-architecture)
  - [Hardware](#hardware)
    - [Power Architecture](#power-architecture)
  - [Wiring](#wiring)
  - [Modules](#modules)
    - [Power Module](#power-module)
    - [Display Module](#display-module)
    - [Audio Module](#audio-module)
    - [Controls Module](#controls-module)
    - [Cooling Module](#cooling-module)
    - [LED Indicator Module](#led-indicator-module)
    - [Volume Buttons Module](#volume-buttons-module)
    - [Game Streaming](#game-streaming)
    - [Dual Boot - Kali Linux](#dual-boot---kali-linux)
  - [Batocera Scripts](#batocera-scripts)
  - [Autostart - custom.sh](#autostart---customsh)
  - [Skills Learned](#skills-learned)
  - [Build Progress](#build-progress)
  - [Repository Structure](#repository-structure)
    - [File Notes](#file-notes)
  - [License](#license)
  - [Author](#author)

---

> A friend's ASUS TUF A15 laptop had a dead motherboard - beyond economic repair, sitting unused. Rather than letting the hardware rot, I took it apart, salvaged everything useful, and started thinking about what to build with it.
>
> The speakers went in. The cooling fans went in. The idea of a portable device that could play games and run security tools started taking shape. A single-board computer made more sense than sourcing a replacement motherboard - smaller, cheaper, GPIO-programmable, and something I had worked with before.
>
> That is where CIPHER started. It has grown considerably since.

---

## What Is This

CIPHER is a custom handheld device built around the Raspberry Pi 5. On the surface it is a retro gaming console - Batocera v42 running emulation across dozens of platforms on a 7-inch display with a custom ESP32-S3 HID controller. Underneath, it is a portable cybersecurity field device - a hidden Kali Linux environment on a secondary SD card, triggerable via a secret button combination on the controller, invisible to anyone who does not know it is there.

Every subsystem is custom-built from scratch - power, audio, display, controls, cooling, LED indicators, volume management, and streaming. No kit. No off-the-shelf handheld. No pre-made controller board. The enclosure is yet to be designed and printed, but all electronics and software are either complete or in active implementation.

The name comes from multiple layers that all fit simultaneously. Retro games are literally encoded data - ciphers of entertainment. Cybersecurity is built on cryptographic ciphers. The hidden Kali environment is itself a secret layer underneath. And personally, Cypher was the agent I mained in Valorant from the early days - dominant, information-focused, and feared in lobbies for the same reasons: knowing more than the opponent and using that knowledge strategically.

The device is currently an active build. Controls, dual boot, and the physical enclosure remain. Everything else is wired, scripted, and running.

---

## System Architecture

```
┌─────────────────────────────────────────────────────┐
│                   CIPHER - Block Diagram            │
├─────────────────────────────────────────────────────┤
│                                                     │
│  [3x NCR18650GA] ──► [3x 1S BMS] ──► [QC4/PD3.0]    │
│        Power Bank Module ──► Pi 5 USB-C (5V/4.5A)   │
│                         └──► AMP (C15 cap tap)      │
│                                                     │
│  Pi 5 ──► [Geekworm X1001] ──► [512GB NVMe SSD]     │
│       ──► [Waveshare 7" HDMI] (Display)             │
│       ──► [PCM5102A I2S DAC] ──► [PAM8403 AMP]      │
│                               ──► [PJ-359B Jack]    │
│                               └──► [TUF Speakers]   │
│       ──► [GPIO] ──► Fan PWM, Volume, LED Strip     │
│       ──► [USB-A] ──► [ESP32-S3 HID Controller]     │
│                                                     │
│  [ESP32-S3] ──► Cherry MX Switches (16 buttons)     │
│             ──► Hall Effect Joysticks (L + R)       │
│             ──► USB HID ──► Batocera (gamepad)      │
│                                                     │
│  Boot selector: ESP32 combo ──► kexec to Kali (SD)  │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

## Hardware

| Component | Model | Purpose |
|---|---|---|
| SBC | Raspberry Pi 5 4GB | Main processor, GPIO, I2S, USB host |
| OS Storage | Geekworm X1001 NVMe HAT + Consistent 512GB M.2 SSD | Fast boot and ROM storage |
| Display | Waveshare 7inch 1024×600 HDMI LCD (H) Capacitive Touch | Primary display |
| Display Cable | Waveshare FFC HDMI 20cm (vertical to straight) | Compact HDMI routing |
| Battery | 3× Panasonic NCR18650GA 3300mAh | Main power cells |
| BMS | 3× 1S 3.7V 6A BMS boards | Individual cell protection |
| Power Module | Dual USB Micro/Type-C 5V 4.5A 22.5W QC4.0 Power Bank Module | PD negotiation, charging, output |
| Power Cable | Hagibis USB4 240W 10cm | Pi 5 PD power delivery |
| DAC | PCM5102A I2S DAC | Digital to analogue audio conversion |
| Amplifier | PAM8403 5V Stereo AMP | Speaker amplification |
| Headphone Jack | PJ-359B 5-pin / 7-pin 3.5mm switching jack | Auto speaker mute on headphone insert |
| Speakers | Salvaged 2 ohms ASUS TUF A15 laptop speakers (left + right) | Stereo audio output |
| Controller MCU | YD-ESP32-S3 N16R8 (16MB flash, 8MB PSRAM) | USB HID gamepad firmware |
| Buttons | Cherry MX Low Profile Red switches ×16 | All controller inputs |
| Joysticks | Hall Effect PS5-type analog modules ×2 | Left and right thumbsticks with L3/R3 |
| Cooling Fans | Salvaged ASUS TUF A15 laptop fans ×2 | Active exhaust cooling |
| LED Indicator | Salvaged ASUS TUF A15 5-LED strip (10-pin FPC) | System status indicators |
| LED Breakout | 10-pin FPC breakout board - 7 Semi | LED strip GPIO interface |
| Volume Buttons | 6×6×8mm tactile switches ×2 on perfboard | Hardware volume control |
| Kali Storage | SanDisk 64 Gb MicroSD card | Kali Linux secondary boot |
| Pentest Modules | Waveshare RP2040-One, RP2350-One, FT232 UART Board | Red/blue team cybersecurity tooling |
| Active Cooler | Raspberry Pi 5 Active Cooler (HAT) | Pi 5 onboard cooling |

<br/>

### Power Architecture

Three NCR18650GA cells, each in its own 18650 holder, each protected by a dedicated 1S BMS board. All three paralleled at a junction point - capacity triples, voltage stays at 3.7V nominal. A red DC rocker switch on the B+ line cuts the entire system. The QC4.0 power bank module handles PD negotiation with the Pi 5 directly via the Hagibis cable on the J5 USB-C port. The S1 button on the module is bridged permanently so the module stays on without manual wake. Charging input comes via a USB-C panel mount → LIRAMARK adapter → module Micro-USB at 15W. A green rocker switch taps the C15 capacitor on the power module to independently control the PAM8403 amplifier.

```
[Cell 1] ──► [1S BMS] ──┐
[Cell 2] ──► [1S BMS] ──┼──► B+ Junction ──► [Red Switch] ──► [QC4/PD 
                                                               Module]
[Cell 3] ──► [1S BMS] ──┘                                             │
                                                        J5 USB-C ──► Pi 5  
                                                          C15 tap ──► AMP
                                             Micro-USB ◄── Charging input
                                                              
```

---

## Wiring

<div align="center">

| | |
|:---:|:---:|
| <img width="340" alt="CIPHER - Power wiring" src="https://github.com/user-attachments/assets/2862fa6f-9187-4a71-8d66-2a6c8d4683dc" /> | <img width="340" alt="CIPHER - Audio wiring" src="https://github.com/user-attachments/assets/1dd5fdb8-af51-4322-a379-c28468d9e187" /> |
| Power module wiring | Audio module wiring |
| <img width="340" alt="CIPHER - Display wiring" src="https://github.com/user-attachments/assets/4d9606f0-d9a7-462a-8e0c-29c51890ffac" /> | <img width="340" alt="CIPHER - LED and Fan wiring" src="https://github.com/user-attachments/assets/d8362b92-fadb-4cdf-99ba-97c06c4f473c" /> |
| Display module wiring | LED and Fan wiring |

</div>

<br/>

> For the full annotated wiring diagram with all modules, pin tables, and connection detail - download the [PDF version](wiring/Cipher%20-%20Wiring%20Diagram.pdf) or view the full interactive whiteboard [HERE](https://canva.link/o7ophpvrg1ajopi)

---

## Modules

### Power Module

**Status: Complete and tested**

<div align="center">

| | |
|:---:|:---:|
| <img width="340" alt="Battery wiring setup 1" src="https://github.com/user-attachments/assets/57e2c51f-5939-44e7-a1d1-f6329b37813e" /> | <img width="340" alt="Battery wiring setup 2" src="https://github.com/user-attachments/assets/79368597-5c23-4368-80bf-8f8e7ac7036b" /> |
| Power stack - assembled | Power stack - alternate view |
| <img width="340" alt="Power bank module front showing all soldering" src="https://github.com/user-attachments/assets/f66c18c5-e234-49b0-9bf4-c8fff46c640b" /> | <img width="340" alt="Gif Battery and charging setup" src="https://github.com/user-attachments/assets/d63ba4be-0049-4c06-bffa-9b57035b814d" /> |
| Power module - soldering detail | Power stack in operation |

</div>

Three Panasonic NCR18650GA cells in individual 18650 holders, each with a dedicated 1S BMS board, paralleled to give ~9900mAh at 3.7V nominal. The QC4.0 power bank module was modified: the S1 button bridged permanently on the PCB underside to prevent the module from auto-shutting off when PD negotiation wasn't fast enough. The PAM8403 amplifier draws power from the C15 capacitor tap on the power module - a direct 5V supply that remains active without needing a device connected to the USB-A ports.

**Approximate battery life at normal gaming load: 2.5–4 hours.** Exact runtime depends on display brightness, streaming vs local emulation, and fan activity.

---

### Display Module

**Status: Complete and tested**

<div align="center">

| | |
|:---:|:---:|
| <img width="340" alt="Testing 7 inch display with Pi 1" src="https://github.com/user-attachments/assets/b6941e74-4017-42fa-861b-993f075ff1cc" /> | <img width="340" alt="Testing 7 inch display with Pi 2" src="https://github.com/user-attachments/assets/3c5b3a61-08ad-4848-b256-d2154d4f062f" /> |
| Display test - playing God of War: Ghost of Sparta | Display and touch both active |

</div>

Waveshare 7-inch 1024×600 HDMI display with capacitive touch. The original plan was a 5-inch DSI display - abandoned when Batocera v42 was found to have no DSI support on the Pi 5. The 7-inch HDMI replacement worked on first connection. Touch is carried over a single USB cable (USB-A on the Pi end, Micro-USB on the display end).

**Current display wiring - 3 cables:**

| Cable | From | To | Purpose |
|---|---|---|---|
| FFC HDMI 20cm | Pi 5 Micro-HDMI | Display HDMI port | Video signal |
| USB-A to Micro-USB | Pi 5 USB-A | Display Micro-USB | Touch data |
| Power line (cap leech) | Power module C16 | Display power input | Display power |

The Pi 5's USB-A ports could not supply enough current to power the display reliably alongside touch. Power is drawn directly from the power module capacitor tap as a workaround. Reducing to a 2-cable solution is an open problem.

For full Batocera config, custom resolution modeline, and troubleshooting - refer to [scripts/Bato Display Setup.pdf](scripts/Bato%20Display%20Setup.pdf)

---

### Audio Module

**Status: Complete and tested**

<div align="center">

| | |
|:---:|:---:|
| <img width="340" alt="Gif Audio test" src="https://github.com/user-attachments/assets/a22ecfde-bb0a-4ca2-a06a-ac9a47582504" /> | <img width="340" alt="Amplifier with capacitor" src="https://github.com/user-attachments/assets/7d79ba6e-4b74-454e-b5a1-3e77ef52b834" /> |
| Full audio stack in operation | AMP with 1000µF stabilisation cap |
| <img width="540" alt="DAC Audio setup test" src="https://github.com/user-attachments/assets/54e9e59f-a688-433c-9328-5d1ad615523a" /> | |
| DAC test - clean audio confirmed | |

</div>

Pi 5 I2S GPIO → PCM5102A DAC → passive Y-split → PAM8403 amplifier and PJ-359B 5-pin switching jack in parallel. When nothing is plugged into the 3.5mm jack, audio routes to the salvaged TUF A15 laptop speakers. When headphones or earphones are inserted, the NC switch inside the jack breaks the speaker signal - automatic switching with no software involvement.

**DAC Wiring:**

| DAC Pin | Pi 5 Physical Pin | BCM | Signal |
|---|---|---|---|
| VIN | Pin 2 | - | 5V power |
| GND | Pin 6 | - | Ground |
| BCK | Pin 12 | BCM 18 | I2S bit clock |
| LCK | Pin 35 | BCM 19 | I2S LR select |
| DIN | Pin 40 | BCM 21 | I2S data |
| SCK | - | - | Internal - front pad bridge only |

**DAC Jumper Configuration (back of board):**

| Jumper | Function | State | Pads |
|---|---|---|---|
| H1L | Filter select | Normal latency | Middle + Right |
| H2L | De-emphasis | Off | Middle + Right |
| H3L | Soft mute (XSMT) | Unmuted HIGH | Middle + **Left** |
| H4L | Audio format | I2S | Middle + Right |
| SCK | Internal clock | PLL enabled | Front two pads bridged |

A 1000µF electrolytic capacitor across the AMP power pins prevents the amplifier from cutting out on sudden loud transients (beat drops, explosions). A 10µF cap between DAC output and AMP input reduces idle hum from the speaker path.

For full config.txt changes, Batocera Pipewire routing, and troubleshooting - refer to [scripts/Bato DAC AMP Setup.pdf](scripts/Bato%20DAC%20AMP%20Setup.pdf)

---

### Controls Module

**Status: Hardware arrived - implementation paused for Security+ exam preparation**

<div align="center">

| | |
|:---:|:---:|
| <img width="340" alt="Cheap gamepad opened - case attached" src="https://github.com/user-attachments/assets/d8710b85-3fa8-44fe-96aa-47159e7b4bba" /> | <img width="340" alt="PS4 Dualshock Ribbon strip" src="https://github.com/user-attachments/assets/8167096b-552a-471a-b7d2-af53a22a2970" /> |
| Original Enter gamepad (test reference) | PS4 JDM-055 ribbon - evaluated and abandoned |

</div>

The controller is a YD-ESP32-S3 N16R8 development board running TinyUSB HID firmware, presenting to the Pi 5 as a standard wired USB gamepad. Batocera recognises it instantly with no drivers required. The ESP32-S3 draws power from the Pi 5 USB-A port - no separate battery required.

**Input hardware - final decisions:**

The original plan used a PS4 JDM-055 ribbon cable and silicone conductive pads for face buttons. This was abandoned - the flexible PCB cannot be soldered without a hotplate, making repairs and modifications impractical. **Cherry MX Low Profile Red switches** were chosen instead - 45cN actuation, 1.2mm pre-travel, 2-pin through-hole compatible with standard perfboard, and individually replaceable. Two packs ordered (20 switches, 4 spare). Custom round keycaps will be 3D printed using standard MX cross stem dimensions.

**Button layout - 16 switches total:**

| Group | Buttons |
|---|---|
| D-pad | Up, Down, Left, Right |
| Face buttons | A, B, X, Y |
| Shoulders | LB, RB, LT, RT |
| Menu | Select, Start, Home, Hotkey |

**Joysticks:** 2× Hall Effect PS5-type analog modules. Strict 3.3V supply required - must not be connected to 5V. L3/R3 are the stick click switches built into each module.

**ESP32-S3 Arduino IDE settings:**

| Setting | Value |
|---|---|
| Board | ESP32S3 Dev Module |
| USB Mode | USB-OTG (TinyUSB) |
| USB CDC On Boot | Disabled |
| Flash Size | 16MB (128Mb) |
| PSRAM | OPI PSRAM |
| Partition Scheme | 16M Flash (3MB APP/9.9MB FATFS) |
| Upload Speed | 921600 |
| CPU Frequency | 240MHz |

---

### Cooling Module

**Status: Script complete and tested - physical integration deferred to enclosure phase**

<div align="center">
  <img width="400" alt="Gif Temperature LED and Fan control" src="https://github.com/user-attachments/assets/89bc74f4-724e-437b-b268-a21b0bc62aa7" />
  <br/>
  <em>Fan and LED temperature response - live test</em>
</div>

<br/>

Two salvaged ASUS TUF A15 laptop fans. Both wired in parallel for power and signal - they behave identically. PWM control was attempted but abandoned: the Pi 5 RP1 chip's PWM overlays are unsupported in Batocera's kernel, and software PWM caused CPU saturation and fan stuttering. Current implementation is simple on/off via `lgpio gpio_write` with temperature hysteresis.

**Temperature thresholds:**
- Fans ON above **65°C**
- Fans OFF below **58°C**
- 7°C hysteresis prevents rapid cycling at the boundary

**Fan Wiring:**

| Fan Cables | Pi 5 Physical Pin | Function |
|---|---|---|
| Both Red wires twisted | Pin 4 | 5V power |
| Both Black wires twisted | Pin 9 | GND |
| Both Blue wires twisted | Pin 32 | PWM signal (connected, not yet used) |
| Fan 1 Yellow wire only | Pin 36 | Tachometer |

For full script and autostart configuration - refer to [scripts/Bato Exhaust Fans Setup.pdf](scripts/Bato%20Exhaust%20Fans%20Setup.pdf)

---

### LED Indicator Module

**Status: Complete and tested**

<div align="center">

| | |
|:---:|:---:|
| <img width="340" alt="Laptop LED indicator strip connected to breakout" src="https://github.com/user-attachments/assets/647797c6-6277-4a3b-a760-adf5697898d9" /> | <img width="340" alt="Gif Indicator Light test" src="https://github.com/user-attachments/assets/1ccbd914-12d2-4b42-9e9a-7ddc026e561e" /> |
| LED strip on 10-pin FPC breakout | LED sequence test - one by one |

</div>

A salvaged 5-LED strip from the friend's ASUS TUF A15, connected via a 10-pin FPC breakout board. The LEDs are active-low - they illuminate when GND is pulled on their control pins, not when voltage is applied. Pins 1+2 twisted together supply 3.3V; Pins 9+10 twisted together provide GND. Each LED is individually GPIO-controlled from the Pi.

**LED Patterns:**

| LED | Colour | Behaviour |
|---|---|---|
| LED 1 | White | Solid = internet connected \| Blink = router only, no internet \| Off = no network |
| LED 2 + LED 3 | Red + White | POST codes on boot \| Thermal warnings at runtime |
| LED 4 | White | Flickers on NVMe read/write activity |
| LED 5 | White | Solid below 60°C \| Slow blink 60–72°C \| Fast blink 72–82°C \| Rapid blink above 82°C |

**POST Codes (Red blinks · White blinks):**

| Code | Meaning |
|---|---|
| R · W | NVMe not found |
| R · WWW | No WiFi interface |
| RR · W | Gamepad not detected |
| RR · WW | Thermal sensor missing |
| RR · WW ×1 | Temperature warning 75°C+ |
| RR · WW ×3 | Temperature critical 85°C+ |

**LED Strip Wiring:**

| FPC Breakout Pin | Pi 5 Physical Pin | Function |
|---|---|---|
| Pin 1 + Pin 2 (twisted) | Pin 1 | 3.3V |
| Pin 9 + Pin 10 (twisted) | Pin 25 | GND |
| Pin 3 + 100Ω resistor | Pin 25* | LED 2 Red - POST/Debug |
| Pin 4 | Pin 13 | LED 3 White - POST/Debug |
| Pin 5 | Pin 11 | LED 1 White - Network status |
| Pin 6 | Pin 29 | LED 5 White - Temperature indicator |
| Pin 7 | Pin 22 | LED 4 White - Storage/NVMe activity |
| Pin 8 | - | NC |

*Pin 3 connects via 100Ω resistor to the same GND rail.

For full script, boot sequence logic, and autostart configuration - refer to [scripts/Bato LED Indicator Setup.pdf](scripts/Bato%20LED%20Indicator%20Setup.pdf)

---

### Volume Buttons Module

**Status: Complete and tested**

<div align="center">

| | |
|:---:|:---:|
| <img width="340" alt="Volume buttons setup" src="https://github.com/user-attachments/assets/4049a94f-920e-41b6-95a7-0d38fc7763db" /> | <img width="340" alt="Gif Volume button test" src="https://github.com/user-attachments/assets/fcd109b5-b168-4e5c-b3c2-3790ae91a993" /> |
| Volume buttons on perfboard | Volume control in operation |

</div>

Two 6×6×8mm tactile switches on a small perfboard. Single press changes volume by 5%. Hold ramps volume continuously. Both pressed simultaneously toggles mute. The script runs as a background daemon via `custom.sh` and uses `batocera-audio` to control the system-wide volume in Batocera - works across all emulators and menus.

**Wiring:**

| Button | Pi 5 Physical Pin | Function |
|---|---|---|
| Button 1 Leg 1 + Button 2 Leg 1 (daisy chain) | Pin 14 | GND |
| Button 1 Leg 2 | Pin 16 | Volume DOWN (BCM 23) |
| Button 2 Leg 2 | Pin 18 | Volume UP (BCM 24) |

For full script and autostart configuration - refer to [scripts/Bato Volume Button Setup.pdf](scripts/Bato%20Volume%20Button%20Setup.pdf)

---

### Game Streaming

**Status: Complete and tested**

<div align="center">
  <img width="700" alt="Game streaming test on Pi via Moonlight on Laptop" src="https://github.com/user-attachments/assets/0f81ff4b-6edd-4fa0-8fdc-86789b5519c5" />
  <br/>
  <em>Hollow Knight streaming from TUF A15 to CIPHER via Moonlight</em>
</div>

<br/>

Moonlight is built into Batocera v42 and appears as a native system in EmulationStation. Sunshine runs on the host laptop (ASUS TUF A15, RTX 3050 with NVENC hardware encoding). The Pi 5 acts as the streaming client - rendering on the laptop, display and input on CIPHER.

**Configuration:**
- Codec: H.264 - the Pi 5 cannot hardware-decode HEVC
- Resolution: 720p/60fps at 15–20 Mbps over local WiFi
- Game entries: created manually as `.moonlight` files in `/userdata/roms/moonlight/`
- Playnite configured on the laptop side for a console-style game library feel

Requires strong local WiFi on both ends. Input and display lag are negligible on a good connection.

---

### Dual Boot - Kali Linux

**Status: Designed - implementation after hardware completion**

The hidden Kali Linux environment boots from a MicroSD card. Normal boot loads Batocera from NVMe as usual. If a secret button combination is held during the boot window, the ESP32-S3 sends a rare HID keycode that a Batocera early-boot service intercepts - triggering a `kexec` into the Kali kernel on the SD card.

This requires zero hardware changes from the completed build. The ESP32 is already the HID controller. The SD card slot is available. Everything is a software implementation task.

The combination is intentionally obscure - something that cannot be pressed accidentally during normal gameplay but is natural to hold intentionally. The Kali install will be lightweight (XFCE or headless) for performance on Pi 5 hardware. The service hooks into `/userdata/system/custom.sh` for persistence across Batocera updates rather than a raw systemd unit.

**Planned cybersecurity tooling (arrived, not yet integrated):**
- Waveshare RP2040-One - red team HID attack payloads
- Waveshare RP2350-One - extended processing for more complex tasks
- Waveshare FT232 USB UART Board - serial communication and debugging

---

## Batocera Scripts

All running scripts live at `/userdata/system/scripts/` and survive Batocera updates via the `custom.sh` hook.

| Script | PDF Reference | What It Does |
|---|---|---|
| `volume_buttons.py` | [Bato Volume Button Setup.pdf](scripts/Bato%20Volume%20Button%20Setup.pdf) | GPIO polling for two physical buttons - single press ±5%, hold ramps, both together mutes. lgpio based with software debounce and thread limiting to prevent ghost triggers from AMP interference |
| `fan_pwm.py` | [Bato Exhaust Fans Setup.pdf](scripts/Bato%20Exhaust%20Fans%20Setup.pdf) | Temperature-based on/off fan control via lgpio gpio_write. Fans on above 65°C, off below 58°C. Logs to `/userdata/system/logs/fan_control.log`. SIGTERM-safe cleanup |
| `led_service.py` | [Bato LED Indicator Setup.pdf](scripts/Bato%20LED%20Indicator%20Setup.pdf) | Three concurrent monitor threads - network state (ping-based), NVMe read/write activity (block stat polling), temperature LED with four brightness states. POST health check sequence on every boot covering NVMe, WiFi, gamepad, and thermal sensor |

Each PDF contains the complete script, wiring reference, setup steps, and a log of problems encountered during implementation and their solutions.

**Note on Batocera version:** All scripts are tested and stable on **Batocera v42**. Version 43 introduced config changes that broke several overlays and Pipewire routing. Staying on v42 until a clean migration path is confirmed.

---

## Autostart - custom.sh

All scripts are launched automatically on boot via Batocera's official autostart hook at `/userdata/system/custom.sh`. This file survives system updates.

```bash
#!/bin/bash
modprobe fuse
bash "/userdata/system/configs/firefox/restore_desktop_entry.sh" &
python3 /userdata/system/scripts/volume_buttons.py &
python3 /userdata/system/scripts/fan_pwm.py &
sleep 5 && python3 /userdata/system/scripts/led_service.py &
```

The `sleep 5` before the LED service gives the system time to fully initialise GPIO and network interfaces before the boot sequence and POST checks run. Without it, early POST checks can report false negatives.

---

## Skills Learned

CIPHER is the most technically broad project I have worked on. It spans domains I had no prior experience in when the build started - and several where I had partial knowledge that turned out to be significantly incomplete.

The full breakdown of what was learned, what surprised me, what failed repeatedly before it worked, and how the skills from one module fed directly into the next is in [My_Journey.md](My_Journey.md).

A summary of domains covered:

- Electronics and power engineering - parallel cell management, BMS theory, PD negotiation, current draw budgeting, capacitor tap power leeching, star grounding
- Analogue audio - I2S protocol, DAC configuration via solder bridges, passive signal splitting, amplifier stabilisation, auto-switching headphone jack behaviour, EMI management
- Linux system administration - Batocera internals, Pipewire audio routing, lgpio, systemd vs custom.sh persistence, SSH-based configuration, kexec dual boot theory
- Embedded firmware - TinyUSB HID descriptor design, ESP32-S3 ADC axis mapping, debounce, deadzone tuning, Hall effect sensor requirements
- Hardware debugging - multimeter probing, serial monitor, hypothesis-driven elimination, reading datasheets and laptop schematics for undocumented hardware

---

## Build Progress

| Module | Status |
|---|---|
| Power stack | ✅ Complete |
| Display | ✅ Complete |
| Audio (DAC + AMP + speakers + jack) | ✅ Complete |
| Volume buttons | ✅ Complete |
| Cooling fans + script | ✅ Complete |
| LED indicator strip + script | ✅ Complete |
| Game streaming (Moonlight + Sunshine) | ✅ Complete |
| NVMe storage (512GB SSD) | ✅ Complete |
| ESP32-S3 HID firmware | 🔧 In progress |
| Cherry MX controller wiring | ⏳ Hardware arrived - paused for Security+ |
| Kali dual boot | ⏳ Planned - after hardware complete |
| Enclosure (Fusion 360 + 3D print) | ⏳ Planned - case design not yet started |

---

## Repository Structure

```
CIPHER/
│
├── scripts/
│   ├── Bato Display Setup.pdf
│   ├── Bato Volume Button Setup.pdf
│   ├── Bato Exhaust Fans Setup.pdf
│   ├── Bato DAC AMP Setup.pdf
│   └── Bato LED Indicator Setup.pdf
│
├── wiring/
│   ├── Cipher - Wiring Diagram.pdf
│   ├── CIPHER - Audio wiring.png
│   ├── CIPHER - LED and Fan wiring.png
│   ├── CIPHER - Display wiring.png
│   └── CIPHER - Power wiring.png
│
├── Electronics - CIPHER - Materials.pdf
├── My_Journey.md
├── LICENSE
└── README.md
```

<br/>

### File Notes

**scripts/** - Each PDF contains the complete Python script, GPIO wiring table, Batocera configuration steps, and a full log of problems encountered and resolved during that module's implementation.

**wiring/Cipher - Wiring Diagram.pdf** - Full annotated wiring diagram covering all modules. Use this or the [Canva whiteboard](https://canva.link/o7ophpvrg1ajopi) for proper zoom and readability. The individual module PNGs embedded above are sections of this complete diagram.

**Electronics - CIPHER - Materials.pdf** - Complete list of materials with supplier sources for every component used in the build.

---

## License

This project is open source under the [MIT License](LICENSE).

You are free to use, modify, and distribute this project for personal or commercial purposes. Attribution appreciated but not required.

---

## Author

**Kewal Shah**

B.E. in Information Technology, self-taught builder, and someone who finds it very difficult to stop once something interesting enough is in front of them.

CIPHER is not a single skill project. It required learning how to manage power at the cell level, route audio through analogue hardware, configure a Linux OS at the system level, write embedded firmware, and debug across hardware and software simultaneously - often in the same afternoon. I did not have most of these skills when the build started. The build is how I acquired them.

I work across embedded systems, cybersecurity, networking, and hardware design. Not by design - by following the next problem wherever it leads. Cybersecurity is where I am going deep right now, holding CompTIA Network+ (814/900) and preparing for Security+. CIPHER is the physical embodiment of that direction: a device that is genuinely useful for security work, built entirely from scratch, that happens to also play retro games.

The CAD and 3D printing skills needed for the enclosure were learned by building a separate device first - a battery-powered countdown timer on an ESP32 with a snap-fit 3D printed case, built in 7 days. That project exists as its own open source repository if you want to see what learning by doing actually looks like: [Countdown Timer V1](https://github.com/Work-KewalShah/Countdown-Timer-V1).

CIPHER is what comes after that.

<br/>

| | |
|---|---|
| GitHub | [@Work-KewalShah](https://github.com/Work-KewalShah) |
| LinkedIn | [Kewal Shah](https://www.linkedin.com/in/kewal-shah-work/) |

<br/>

> *"Retro gaming on the surface. Something else underneath. Just like the name."*

---

<div align="center">

**If this build helped you or gave you ideas for your own cyberdeck, consider leaving a ⭐ on the repository.**

</div>
