# eBUS Synchronisation

Communication via the eBUS requires synchronisation.
This project should enable this synchronisation although transmission latency (e. g. via WiFi).

__Overview:__
 <!--* [Introduction](#introduction)-->
 * [What You Need](#what-you-need)
 * [Wiring](#wiring)
 * [Firmware](#firmware)
 * [Evaluation](#evaluation)
 * [Other Projects](#other-projects)
   - [ebusd-esp](#ebusd-esp)
* [Serial Bridge Projects](#serial-bridge-projects)
* [References](#references)

<!--## Introduction-->

## What You Need

- **eBUS Adapter**
- **Microcontroller board.** I successfully tested the Blue Pill Board with a STM32F103C8T6 MCU.
- **Development environment** (e. g. [Arduino IDE](https://www.arduino.cc/en/software) or [Visual Studio Code](https://code.visualstudio.com/) with [PlatformIO](https://platformio.org/install/ide?install=vscode))

**eBUS Adapter:** Any eBUS adapter with a UART interface should be fine.

**Microcontroller board:** The STM32F103C8T6 achieved a good timing accuracy also with quite some debug output. I also tested a ATmega328P with 8&nbsp;MHz, which was too slow. At 16&nbsp;MHz it was slightly better, but with Arduino and quite some debug output it was also too slow (an optimised native implementation should be fine).

## Wiring

Connect Vcc and GND.  
eBUS TxD - A0 (SENSE_PIN)\
eBUS RxD - A1 (WRITE_PIN)

eBUS adapter (example):\
![MCU pinning](doc/images/eBUS-Adapter_GH.jpg?raw=true)

STM32F103C8T6 board:\
![MCU pinning](doc/images/STM32F103C8T6_pins.JPG?raw=true)

## Firmware

### Flashing the bootloader:

[Flashing Bootloader for STM32F103C8T BluePill Boards](https://github.com/rogerclarkmelbourne/Arduino_STM32/wiki/Flashing-Bootloader-for-BluePill-Boards)

The firmware can then be easily uploaded via USB by the "STM32duino bootloader".

### Synchronisation-Firmware

My test software (ugly draft, not released yet) buffers the data which should be sent to the eBUS and transmits it after a SYN symbol, as required by the specification.
It should also detect collisions and pre-process received data before forwarding.

I have chosen Arduino to be able to easily test on different platforms. Since no special libraries are used, it should be rather easy to natively port the code to a particular platform.

What I observed on the Arduino IDE with the STM32F103C8T6 (CPU Speed configured at 72&nbsp;MHz):
The timer micros() works as expected, but delayMicroseconds() only delays half of the time as requested. To fix this, I double the delay value.

### To-Do's:
* Support of eBUS daemon's [enhanced protocol](https://github.com/john30/ebusd/blob/master/docs/enhanced_proto.md)

## Evaluation

A logic analyser trace shows quite nice timings:
![MCU pinning](doc/images/LogicSend.png?raw=true)

## Other Projects

### ebusd-esp

The (closed-source) firmware [ebusd-esp](http://github.com/john30/ebusd-esp) synchronises to the SYN symbol, but has quite some latency:
![MCU pinning](doc/images/LogicSend_ebusd-esp.png?raw=true)
Wireshark shows that a lot of packets are sent with just one byte of payload:
![Wireshark ScreenShot](doc/images/WireShark_ebusd-esp.png?raw=true)

## Serial Bridge Projects
[Untested]
* [ESP-LINK: Wifi-Serial Bridge with REST&MQTT](https://github.com/jeelabs/esp-link)
* [Tasmota Serial to TCP Bridge](https://tasmota.github.io/docs/Serial-to-TCP-Bridge/)
* [ESPHome UART](https://esphome.io/components/uart.html)
* [ESP32-Serial-Bridge](https://github.com/AlphaLima/ESP32-Serial-Bridge)

## References
* [eBUS - English Wikipedia article](https://en.wikipedia.org/wiki/EBUS_(serial_buses))
* [eBUS - German Wikipedia article](https://de.wikipedia.org/wiki/EBus)
* [openHAB eBUS binding (outdated)](https://v2.openhab.org/addons/bindings/ebus1/)
* ["Unofficial" openHAB eBUS binding](https://github.com/csowada/openhab-ebus-binding)
* [Description of "unofficial" openHAB eBUS binding](https://github.com/csowada/openhab-ebus-binding/blob/main/bundles/org.openhab.binding.ebus/README.md)
* [eBUS daemon](https://github.com/john30/ebusd)
* [Firmware for ESP8266 and ESP32 allowing eBUS communication for ebusd](https://github.com/john30/ebusd-esp)
* [FHEM eBUS wiki article (German)](https://wiki.fhem.de/wiki/EBUS)
* [eBUS adapter design which does not require trimming](https://ebus.github.io/adapter/base.en.html)
* Optional: Logic analyser software (I used [sigrok]([https://ebus.github.io/adapter/base.en.html](https://sigrok.org/wiki/Main_Page)) and PulseView GUI)
