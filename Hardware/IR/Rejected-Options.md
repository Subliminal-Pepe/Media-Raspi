---
title: "Rejected Options"
aliases:
  - Rejected Hardware
tags:
  - hardware/ir
  - status/rejected
created: 2026-11-22
---

# Rejected Options

A short note on hardware that was considered and ruled out, so the decision is not re-litigated later.

## FLIRC USB

- Well reviewed and sold on Amazon.
- **Receive only** — it turns IR remotes into keyboard input. It cannot transmit IR, so it cannot wake the amp.
- See also: most generic "USB IR receivers" are receive-only HID devices.

## USB IrDA dongles

- LIRC's own FAQ states these **cannot** be used for LIRC — the IrDA protocol is technically different from consumer IR remote control.
- Many cheap marketplace "IR blasters" are actually IrDA or HID receivers.

## No-name marketplace "USB IR blaster" sticks

- Typically need Windows-only software, present as HID keyboards, or are receive-only.
- The user's explicit feedback: these options were "shit or on very sketchy websites". That ruled out the small-vendor USB category entirely.

## Tira / CommandIR

- €100+ and/or hard to find. Overkill and scarce.

## IguanaWorks USB IR Transceiver

- Historically a good LIRC-compatible device with adjustable 25–125 kHz carriers.
- **Company has closed**; products are no longer sold. Documentation remains online only.

## Hand-built GPIO LED circuit

- A bare IR LED + resistor + transistor wired to a Pi GPIO would work and is well documented.
- The user explicitly preferred **ready-made modules** over hand-building a circuit.
- Replaced by the plug-in Adafruit STEMMA modules in [[Adafruit-GPIO-IR-SELECTED]].

## CEC / HDMI Control as the wake mechanism

- The RX-V371 does support HDMI Control, but it requires an HDMI connection to a TV.
- The project requires the TV to stay off and uninvolved. Rejected at the architecture stage.

## See also

[[Hardware-Options-Comparison]] · [[Irdroid-USB-IR-Transceiver]] · [[USB-UIRT]] · [[Hauppauge-MCE-USB]] · [[PCBWay-Pico-IR-Blaster-HAT]] · [[06-Decision-Log]]
