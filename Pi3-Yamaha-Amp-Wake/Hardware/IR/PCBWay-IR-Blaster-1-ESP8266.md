---
title: "PCBWay IR Blaster 1 (ESP8266)"
aliases:
  - IR Blaster 1
  - ESP8266 IR Blaster
tags:
  - hardware/ir
  - status/alternative
created: 2026-11-22
---

# PCBWay — IR Blaster 1 (ESP8266)

Second project page evaluated: `https://www.pcbway.com/project/shareproject/IR_Blaster_1.html`
Associated write-up: settorezero.com article on the "IR Blaster" Wi-Fi remote.

## What it is

A complete, documented standalone Wi-Fi IR blaster:

- Gerbers, BOM, source code, and a 3D-printable case published
- 6× TSAL6100 IR LEDs on 3× 2N2222 transistors, driven from 5 V — very strong output
- Firmware is the `IRMQTTServer` example from **IRRemoteESP8266**
- Learning supported by attaching a cheap **IR receiver to GPIO2** (3-wire: 3.3 V, GND, signal)
- Supports raw, Pronto, and GlobalCache formats
- Mostly through-hole; the only SMD part is an LM1117-3.3 regulator in SOT-223

## Pros

- Genuinely complete project with documentation — better than the Pico HAT.
- The Pi 3 just makes an HTTP request, e.g. `curl 'http://<blaster-ip>/ir?...'`, or publishes MQTT. No USB protocol to invent.
- Very strong IR output.

## Cons

- Needs its own **5 V supply (≥700 mA)** — a phone charger works.
- The **ESP-01S must be flashed** once with a 3.3 V USB-UART adapter. Get the ESP-01S (1 MB flash), not the older ESP-01.
- Initial Wi-Fi setup via a captive portal, plus a DHCP reservation or static IP.
- The PCB does not include a receiver — it is a 3-wire add-on for learning.
- More moving parts than the GPIO approach.

## Verdict

The best of the two PCBWay projects, and a good design overall. It lost to the Adafruit GPIO option because the user preferred mainstream parts and no firmware flashing or extra network device.

## How the plan would change

`amp-wake.sh` would call `curl` against the blaster's HTTP endpoint instead of `ir-ctl`. See [[Hardware-Options-Comparison]].

## Related

[[Hardware-Options-Comparison]] · [[PCBWay-Pico-IR-Blaster-HAT]] · [[Research-Log]]
