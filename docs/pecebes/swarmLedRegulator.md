---
tags:
    - cheatSheets
    - swarmLedRegulator
---

# Swarm Led Regulator PCB

### ESP8266
We use an ESP8266 (FCC ID: 2AHMR-ESP07S)

PX4 Docs: https://docs.px4.io/main/en/telemetry/esp8266_wifi_module

User Manual: https://www.laskakit.cz/user/related_files/esp-07s_product_specification_en.pdf

FCC Report: https://fcc.report/FCC-ID/2AHMR-ESP07S

### Power

5A step down dc-dc converter (https://www.ti.com/lit/ds/symlink/tps54560b.pdf). Implemented as the typical Application, with some inductance changes.
An AMS1117-3.3 in cascade with the 5V buck.

Feature: 
- 5VP_ON status led for 5V Buck output.
- 3V3_ON status led for 3.3V Buck output.

### NeoPixel

SN74LVC1T45 logic level converter  for stepping 3.3V signal into 5V.

The WS2811 is solely the LED controller, so the *__WS2812B__* is going to be used for ease of use, it is an IC with the RGB LED and the controller embedded.

__*WS2812B* part for LCSC = C2874885__
