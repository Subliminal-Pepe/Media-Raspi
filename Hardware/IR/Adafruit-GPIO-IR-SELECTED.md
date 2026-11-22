---
title: "Adafruit GPIO IR (SELECTED)"
aliases:
  - Adafruit GPIO IR
  - Selected IR Hardware
  - 5639 and 5939
tags:
  - hardware/ir
  - status/selected
created: 2026-11-22
---

# Adafruit GPIO IR — SELECTED

## What it is

Two small breakout boards from Adafruit, both with plug-in **STEMMA JST PH 2 mm** connectors:

| Part | Function | Key specs |
|---|---|---|
| **5639** — High Power Infrared IR LED Emitter | Transmit | Multiple IR LEDs, onboard FET driver, ~100–200 mA pulsed, 10 m+ range, V+ 3–5 V, signal 3.3 V-safe |
| **5939** — Infrared IR Remote Receiver | Receive / learn | 38 kHz demodulated receiver, 3–5 V, logic-level output |

Also stocked by Pimoroni and Digi-Key. Approximate total: **$12**.

## Why it was selected

- **Reputable retailer** — Adafruit has documentation, forums, and a return policy. This directly addressed the user's rejection of "sketchy websites".
- **Mainline Linux support** — `gpio-ir` and `gpio-ir-tx` device-tree overlays plus `ir-ctl` from `ir-tools`. No vendor driver, no firmware to write, no cloud service.
- **Both directions** — learn the code from the original remote with the receiver, replay with the emitter. No extra hardware needed later.
- **Plug-in cables** — satisfies "ready-made module" preference without hand-building a transistor + LED circuit.
- **Strong output** — 10 m+ range gives plenty of margin for an amp across the room.
- **Fully local** — no phone app or account needed during operation.

## What it involves

- Two boards wired to the Pi GPIO (see [[Wiring-Details]]).
- Two device-tree overlays, used one at a time for learning vs transmitting.
- `ir-ctl --receive` to capture the SCENE code, then `ir-ctl --send` to replay it.
- No extra power supply — the Pi's 5 V rail powers the emitter.

## Trade-offs

- Slightly more setup than a consumer Wi-Fi blaster (overlays and `ir-ctl`).
- The learning/transmitting overlay swap is a small extra step (see [[IR-Learning-and-Sending]]).
- Requires a decent 2.5 A Pi supply.

## Alternatives

- [[Adafruit-IR-Transceiver-5990]] — one board instead of two, lower transmit range.
- [[BroadLink-RM4-Mini]] — no GPIO wiring, but app/cloud setup.
- [[GlobalCache-iTach-IP2IR]] — professional reliability at 10× the price.
- [[Hardware-Options-Comparison]] — full matrix.

## Related

[[Home]] · [[03-Implementation-Plan]] · [[Wiring-Details]] · [[IR-Learning-and-Sending]] · [[06-Decision-Log]]
