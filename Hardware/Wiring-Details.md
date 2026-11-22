---
title: "Wiring Details"
aliases:
  - Wiring
  - GPIO Pinout
tags:
  - hardware/ir
  - wiring
created: 2026-11-22
---

# Wiring Details

All wiring uses plug-in JST PH 2 mm cables with female dupont jumpers to the Pi 3's 40-pin header. No soldering.

> [!info] Pin numbering
> Physical pin numbers are used throughout (1–40), not BCM/GPIO numbers unless stated.

## Receiver — Adafruit 5939 (learning phase)

| Board pin | Pi physical pin | Signal |
|---|---|---|
| V+ | **1** | 3.3 V |
| GND | **6** | Ground |
| OUT / S | **8** | GPIO14 (BCM) |
| (4th) | — | unused |

Overlay: `dtoverlay=gpio-ir,gpio_pin=14`

The receiver is a demodulated 38 kHz sensor, so it runs happily at 3.3 V and outputs a logic-level signal suitable for a GPIO input.

## Emitter — Adafruit 5639 (production phase)

| Board pin | Pi physical pin | Signal |
|---|---|---|
| V+ | **2 or 4** | **5 V** |
| GND | **6** (or any GND) | Ground |
| Signal | **11** | GPIO17 (BCM) |
| (4th) | — | unused |

Overlay: `dtoverlay=gpio-ir-tx,gpio_pin=17`

> [!danger] Power rule
> V+ **must** be 5 V, from a good 2.5 A supply. The IR LEDs pulse at roughly 100–200 mA. Running them from the Pi's 3.3 V rail risks brownouts. The signal pin is 3.3 V-safe because the board uses an onboard N-channel FET driver. All grounds must be common.

## Keeping both boards attached

If you choose to leave both connected instead of swapping:

- Receiver V+ → 3.3 V, OUT → GPIO14
- Emitter V+ → 5 V, Signal → GPIO17, GND shared
- Load **both** overlay lines in `config.txt`:

```text
dtoverlay=gpio-ir,gpio_pin=14
dtoverlay=gpio-ir-tx,gpio_pin=17
```

- Identify devices with `ir-keytable`; the receiver frequently appears as `/dev/lirc1`. Set `LIRC_DEV` in [[Amp-Wake-Script]] to the transmitter device.
- Be aware of the documented conflicts described in [[05-Troubleshooting]].

## Audio cable

- 3.5 mm stereo plug → the Pi's headphone jack.
- Two RCA plugs (red/white) → RX-V371 **AUDIO1** input.
- Keep the cable away from the GPIO wiring to reduce noise (analog audio is sensitive).

## Physical placement

- Emitter needs a clear line of sight to the amp's **front IR window**.
- If the amp is in a closed cabinet, the high-power emitter may still work via reflections, but moving it to the shelf edge is more reliable.
- Receiver only needs line of sight temporarily, during the one-time learning step.

## Related

[[Adafruit-GPIO-IR-SELECTED]] · [[IR-Learning-and-Sending]] · [[03-Implementation-Plan]] · [[Pi-Base-Configuration]]
