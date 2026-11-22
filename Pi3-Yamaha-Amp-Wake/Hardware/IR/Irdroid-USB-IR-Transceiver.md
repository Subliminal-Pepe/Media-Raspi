---
title: "Irdroid USB IR Transceiver"
aliases:
  - Irdroid
tags:
  - hardware/ir
  - status/rejected
created: 2026-11-22
---

# Irdroid USB IR Transceiver

A small-vendor USB IR transceiver, found at irdroid.eu / irdroid.com.

## Versions found

| Version | Price | Notes |
|---|---|---|
| USB Infrared Transceiver v3 | €32 | TX + RX, 11–56 kHz, 3 LEDs, 10 m+, USB CDC-ACM, 940 nm |
| USB Infrared Transceiver kit | €36 | Older PIC18F2550 design, 3 LEDs, 38 kHz receiver, open hardware |

## Technical appeal

- Transmit **and** receive in one unit.
- USB CDC-ACM, and since Linux kernel **5.8** the mainline IRToy driver auto-loads and presents `/dev/lirc0` — genuinely plug-and-play on a modern Pi OS.
- Compatible with `ir-ctl` / `irsend`, so no vendor protocol needed.
- No external power supply.

## Why rejected

The user explicitly rejected these options as "shit or on very sketchy websites". The storefront is a small Bulgarian/EU hobby vendor rather than a mainstream retailer, so support and return confidence are low. Functionally it would have worked well.

## Verdict

Technically sound, commercially not trusted for this build. Superseded by [[Adafruit-GPIO-IR-SELECTED]].

## Related

[[Hardware-Options-Comparison]] · [[Rejected-Options]] · [[Research-Log]]
