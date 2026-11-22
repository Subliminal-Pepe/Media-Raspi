---
title: "Adafruit IR Remote Transceiver (5990)"
aliases:
  - Adafruit 5990
  - IR Transceiver
tags:
  - hardware/ir
  - status/alternative
created: 2026-11-22
---

# Adafruit IR Remote Transceiver — product 5990

A single STEMMA JST PH board that does **both** transmit and receive in one solder-free package:

- 940 nm IR emitter
- 38 kHz receiver
- 4-pin JST PH cable, breakout pads
- Sold by Adafruit and Pimoroni; also listed at DigiKey

Approximate price: $5–7.

## Pros

- One board and one cable instead of two.
- Same reputable supplier and same mainline Linux overlay approach.
- Covers both learning and transmitting.

## Cons

- The dedicated **5639 high-power emitter has four LEDs** and a much stronger output. For reliability — especially if the amp is in a cabinet or off-axis — the separate high-power emitter is the safer choice.
- A single combined board means the IR source and sensor are close together; placement needs a little more thought.

## Verdict

Viable and tidy, but the two-board combo ([[Adafruit-GPIO-IR-SELECTED]]) was preferred for transmit range. If your amp is very close with clear line of sight, this is a perfectly reasonable simplification.

## Related

[[Hardware-Options-Comparison]] · [[Adafruit-GPIO-IR-SELECTED]] · [[Rejected-Options]]
