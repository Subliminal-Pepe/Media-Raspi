---
title: "Hardware Options Comparison"
aliases:
  - Comparison
  - Hardware Comparison
tags:
  - hardware/ir
  - comparison
  - moc
created: 2026-11-22
---

# Hardware Options Comparison

Every IR control option investigated for waking the RX-V371. The requirement throughout: **send one IR code from a Raspberry Pi 3 to the amp's front IR window**, ideally with receive capability for learning.

## Summary table

| Option | TX | RX (learning) | Pi 3 interface | Firmware / app | Retailer confidence | Price | Verdict |
|---|---|---|---|---|---|---|---|
| **Adafruit GPIO emitter + receiver (5639 + 5939)** | Yes | Yes | GPIO + `ir-ctl` | None | **High** (Adafruit/Pimoroni/DigiKey) | ~$12 | ✅ **SELECTED** |
| Adafruit 5990 IR Transceiver | Yes | Yes | GPIO + `ir-ctl` | None | High | ~$5–7 | 🔶 Alternative (lower TX range) |
| BroadLink RM4 Mini | Yes | Yes | Wi-Fi + `python-broadlink` | Phone app for setup | High (Amazon) | ~$20–25 | 🔶 Easy but app/cloud setup |
| Global Cache iTach IP2IR | Yes | Yes (built-in learner) | Ethernet + TCP | None | High (pro AV, Amazon) | ~$100–140 | 🔶 Bulletproof but overkill |
| PCBWay "IR Blaster 1" (ESP8266) | Yes (6 LEDs) | Add-on receiver | Wi-Fi + HTTP/MQTT | Flash ESP-01S | Medium (project page) | Parts cost | 🔶 Good design, more work |
| PCBWay Pico IR Blaster HAT | Yes (4 LEDs) | Yes (TSOP38238) | Pico firmware or pad wiring | Write Pico firmware | Medium (project page) | Bare PCB | ❌ Not a Pi 3 drop-in |
| Irdroid USB IR Transceiver v3 | Yes | Yes | USB → `/dev/lirc0` | None (>kernel 5.8) | Low/Medium (small vendor) | €32 | ❌ Small vendor |
| USB-UIRT | Yes | Yes | USB → LIRC | None | Medium (direct order) | $55 + shipping | ❌ Windows driver sunset noise |
| Hauppauge MCE kit 01069 | Yes* | Yes* | USB `mceusb` | None | High | ~$29.95 | ❌ Must verify blaster inclusion |
| FLIRC USB | No | Yes | USB HID | None | High | ~$30 | ❌ Receive only |
| USB IrDA dongles | No | No | — | — | — | — | ❌ LIRC says impossible |
| Cheap marketplace "USB IR blaster" | Usually no | Usually yes | HID | Windows app | Low | ~$10 | ❌ Mislabelled receivers |

\* Many MCE kits on the market are receive-only; the Hauppauge 01069 specifically lists a receiver/blaster beanbag.

## Why Adafruit GPIO won

1. **Trustworthy supply** — established retailer with returns and documentation.
2. **Standard Linux path** — `gpio-ir` / `gpio-ir-tx` overlays and `ir-ctl` are in the mainline kernel; no vendor driver or firmware.
3. **Both TX and RX** — learn from the original remote and replay, without a second device.
4. **No cloud, no app, no extra IP device** in normal operation.
5. **Plug-in STEMMA/JST cables** — no soldering, but not a bare-component circuit.
6. **Sufficient range** — the high-power emitter is rated for 10 m+.

The trade-off is a small amount of GPIO wiring and one overlay swap, which is acceptable.

## Decision trail

The research went through several rounds: the two PCBWay boards were evaluated first; then a search for ready-made USB devices; then a user rejection of niche/sketchy vendors; then mainstream options (BroadLink, Global Cache, Adafruit/Pimoroni); finally the user chose the Adafruit/Pimoroni GPIO route. See [[Research-Log]] and [[06-Decision-Log]].

## Detailed notes

- [[Adafruit-GPIO-IR-SELECTED]] — chosen
- [[Adafruit-IR-Transceiver-5990]] — one-board alternative
- [[BroadLink-RM4-Mini]]
- [[GlobalCache-iTach-IP2IR]]
- [[PCBWay-Pico-IR-Blaster-HAT]]
- [[PCBWay-IR-Blaster-1-ESP8266]]
- [[Irdroid-USB-IR-Transceiver]]
- [[USB-UIRT]]
- [[Hauppauge-MCE-USB]]
- [[Rejected-Options]]
- [[Wiring-Details]]
