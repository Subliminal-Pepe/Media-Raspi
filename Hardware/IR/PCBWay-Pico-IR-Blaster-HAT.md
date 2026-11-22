---
title: "PCBWay Pico IR Blaster HAT"
aliases:
  - Pico IR Blaster HAT
tags:
  - hardware/ir
  - status/rejected
created: 2026-11-22
---

# PCBWay — Raspberry Pi Pico IR Blaster HAT

First project page evaluated: `https://www.pcbway.com/project/shareproject/Raspberry_Pi_Pico_IR_Blaster_HAT.html`

## What it actually is

A bare PCB designed for a **Raspberry Pi Pico**, not a Pi 3 HAT:

- 4× TSAL6400 IR LEDs on `GP6`, driven through a BSS138
- TSOP38238 IR receiver on `GP5`
- DS18B20 temperature sensor on `GP4`
- 0603 SMD parts
- Up to ~400 mA LED current
- **No firmware published on the page**

## Pros

- Includes both emitters and a receiver.
- Cheap bare boards from PCBWay.

## Cons

- Pico-specific — not a drop-in for the Pi 3's 40-pin header.
- Would require writing custom Pico firmware, or manually wiring pads to Pi GPIO.
- 0603 SMD soldering.
- No documented code or setup instructions.

## Verdict

Rejected. Usable only with a Pico and custom firmware, or by wiring pads directly to Pi GPIO — i.e. more work than the purpose-built Adafruit modules.

## Related

[[Hardware-Options-Comparison]] · [[PCBWay-IR-Blaster-1-ESP8266]] · [[Research-Log]]
